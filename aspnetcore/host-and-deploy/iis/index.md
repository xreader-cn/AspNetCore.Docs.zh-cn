---
<span data-ttu-id="31a85-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-102">'Blazor'</span></span>
- <span data-ttu-id="31a85-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-103">'Identity'</span></span>
- <span data-ttu-id="31a85-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-105">'Razor'</span></span>
- <span data-ttu-id="31a85-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="31a85-107">使用 IIS 在 Windows 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31a85-107">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="31a85-108">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="31a85-108">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="31a85-109">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="31a85-109">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="31a85-110">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="31a85-110">Supported operating systems</span></span>

<span data-ttu-id="31a85-111">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="31a85-111">The following operating systems are supported:</span></span>

* <span data-ttu-id="31a85-112">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-112">Windows 7 or later</span></span>
* <span data-ttu-id="31a85-113">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-113">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="31a85-114">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="31a85-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="31a85-115">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="31a85-115">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="31a85-116">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="31a85-116">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="31a85-117">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="31a85-117">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="31a85-118">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="31a85-118">Supported platforms</span></span>

<span data-ttu-id="31a85-119">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-119">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="31a85-120">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="31a85-120">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="31a85-121">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="31a85-121">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="31a85-122">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="31a85-122">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="31a85-123">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="31a85-123">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="31a85-124">为 32 位 (x86) 发布的应用必须已为其 IIS 应用程序池启用 32 位。</span><span class="sxs-lookup"><span data-stu-id="31a85-124">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="31a85-125">有关详细信息，请参阅[创建 IIS 站点](#create-the-iis-site)部分。</span><span class="sxs-lookup"><span data-stu-id="31a85-125">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="31a85-126">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-126">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="31a85-127">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-127">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="31a85-128">托管模型</span><span class="sxs-lookup"><span data-stu-id="31a85-128">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="31a85-129">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="31a85-129">In-process hosting model</span></span>

<span data-ttu-id="31a85-130">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="31a85-131">进程内承载相较进程外承载提供更优的性能，因为请求并不通过环回适配器进行代理，环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="31a85-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="31a85-132">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="31a85-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="31a85-133">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="31a85-133">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="31a85-134">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="31a85-134">Performs app initialization.</span></span>
  * <span data-ttu-id="31a85-135">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="31a85-135">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="31a85-136">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="31a85-136">Calls `Program.Main`.</span></span>
* <span data-ttu-id="31a85-137">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="31a85-137">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="31a85-138">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="31a85-138">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="31a85-140">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-140">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="31a85-141">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="31a85-141">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="31a85-142">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="31a85-142">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="31a85-143">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="31a85-143">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="31a85-144">在 IIS HTTP 服务器处理请求后：</span><span class="sxs-lookup"><span data-stu-id="31a85-144">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="31a85-145">请求被发送到 ASP.NET Core 中间件管道。</span><span class="sxs-lookup"><span data-stu-id="31a85-145">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="31a85-146">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="31a85-146">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="31a85-147">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="31a85-147">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="31a85-148">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="31a85-148">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="31a85-149">对于现有应用，进程内托管是可选功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-149">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="31a85-150">ASP.NET Core Web 模板使用进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="31a85-150">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="31a85-151">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。 </span><span class="sxs-lookup"><span data-stu-id="31a85-151">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="31a85-152">性能测试表明，与在进程外托管应用并将请求代理传入 [Kestrel](xref:fundamentals/servers/kestrel) 相比，在进程中托管 .NET Core 应用可提供明显更高的请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="31a85-152">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="31a85-153">作为单个文件可执行文件发布的应用无法由进程内托管模型加载。</span><span class="sxs-lookup"><span data-stu-id="31a85-153">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="31a85-154">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="31a85-154">Out-of-process hosting model</span></span>

<span data-ttu-id="31a85-155">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="31a85-155">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="31a85-156">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-156">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="31a85-157">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="31a85-157">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="31a85-158">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="31a85-158">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="31a85-160">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-160">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="31a85-161">驱动程序将请求路由到网站的配置端口上的 IIS。</span><span class="sxs-lookup"><span data-stu-id="31a85-161">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="31a85-162">配置的端口通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="31a85-162">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="31a85-163">此模块将该请求转发到应用的随机端口上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="31a85-163">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="31a85-164">随机端口不是 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="31a85-164">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="31a85-165">ASP.NET Core 模块在启动时通过环境变量指定端口。</span><span class="sxs-lookup"><span data-stu-id="31a85-165">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="31a85-166"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="31a85-166">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="31a85-167">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-167">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="31a85-168">此模块不支持 HTTPS 转发。</span><span class="sxs-lookup"><span data-stu-id="31a85-168">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="31a85-169">即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="31a85-169">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="31a85-170">Kestrel 从模块获取请求后，请求会被转发到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="31a85-170">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="31a85-171">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="31a85-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="31a85-172">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="31a85-172">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="31a85-173">应用的响应传递回 IIS，IIS 将响应转发回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="31a85-173">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="31a85-174">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-174">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="31a85-175">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="31a85-175">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="31a85-176">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="31a85-176">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="31a85-177">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="31a85-177">Enable the IISIntegration components</span></span>

<span data-ttu-id="31a85-178">在 `CreateHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="31a85-178">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="31a85-179">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/generic-host#default-builder-settings>。</span><span class="sxs-lookup"><span data-stu-id="31a85-179">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="31a85-180">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="31a85-180">IIS options</span></span>

<span data-ttu-id="31a85-181">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="31a85-181">**In-process hosting model**</span></span>

<span data-ttu-id="31a85-182">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-182">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="31a85-183">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="31a85-183">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="31a85-184">选项</span><span class="sxs-lookup"><span data-stu-id="31a85-184">Option</span></span>                         | <span data-ttu-id="31a85-185">默认</span><span class="sxs-lookup"><span data-stu-id="31a85-185">Default</span></span> | <span data-ttu-id="31a85-186">设置</span><span class="sxs-lookup"><span data-stu-id="31a85-186">Setting</span></span> |
| ---
<span data-ttu-id="31a85-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-188">'Blazor'</span></span>
- <span data-ttu-id="31a85-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-189">'Identity'</span></span>
- <span data-ttu-id="31a85-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-191">'Razor'</span></span>
- <span data-ttu-id="31a85-192">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-194">'Blazor'</span></span>
- <span data-ttu-id="31a85-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-195">'Identity'</span></span>
- <span data-ttu-id="31a85-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-197">'Razor'</span></span>
- <span data-ttu-id="31a85-198">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-200">'Blazor'</span></span>
- <span data-ttu-id="31a85-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-201">'Identity'</span></span>
- <span data-ttu-id="31a85-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-203">'Razor'</span></span>
- <span data-ttu-id="31a85-204">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-206">'Blazor'</span></span>
- <span data-ttu-id="31a85-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-207">'Identity'</span></span>
- <span data-ttu-id="31a85-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-209">'Razor'</span></span>
- <span data-ttu-id="31a85-210">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-212">'Blazor'</span></span>
- <span data-ttu-id="31a85-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-213">'Identity'</span></span>
- <span data-ttu-id="31a85-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-215">'Razor'</span></span>
- <span data-ttu-id="31a85-216">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-218">'Blazor'</span></span>
- <span data-ttu-id="31a85-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-219">'Identity'</span></span>
- <span data-ttu-id="31a85-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-221">'Razor'</span></span>
- <span data-ttu-id="31a85-222">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-224">'Blazor'</span></span>
- <span data-ttu-id="31a85-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-225">'Identity'</span></span>
- <span data-ttu-id="31a85-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-227">'Razor'</span></span>
- <span data-ttu-id="31a85-228">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-230">'Blazor'</span></span>
- <span data-ttu-id="31a85-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-231">'Identity'</span></span>
- <span data-ttu-id="31a85-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-233">'Razor'</span></span>
- <span data-ttu-id="31a85-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-236">'Blazor'</span></span>
- <span data-ttu-id="31a85-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-237">'Identity'</span></span>
- <span data-ttu-id="31a85-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-239">'Razor'</span></span>
- <span data-ttu-id="31a85-240">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-242">'Blazor'</span></span>
- <span data-ttu-id="31a85-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-243">'Identity'</span></span>
- <span data-ttu-id="31a85-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-245">'Razor'</span></span>
- <span data-ttu-id="31a85-246">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-248">'Blazor'</span></span>
- <span data-ttu-id="31a85-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-249">'Identity'</span></span>
- <span data-ttu-id="31a85-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-251">'Razor'</span></span>
- <span data-ttu-id="31a85-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-254">'Blazor'</span></span>
- <span data-ttu-id="31a85-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-255">'Identity'</span></span>
- <span data-ttu-id="31a85-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-257">'Razor'</span></span>
- <span data-ttu-id="31a85-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-260">'Blazor'</span></span>
- <span data-ttu-id="31a85-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-261">'Identity'</span></span>
- <span data-ttu-id="31a85-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-263">'Razor'</span></span>
- <span data-ttu-id="31a85-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-264">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-266">'Blazor'</span></span>
- <span data-ttu-id="31a85-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-267">'Identity'</span></span>
- <span data-ttu-id="31a85-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-269">'Razor'</span></span>
- <span data-ttu-id="31a85-270">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-270">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-271">---- | | `AutomaticAuthentication`      | `true`  | 如果为 `true`，IIS 服务器会将 `HttpContext.User` 设置为进行 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-271">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="31a85-272">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="31a85-272">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="31a85-273">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-273">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="31a85-274">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-274">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="31a85-275">| | `AuthenticationDisplayName`    | `null`  | 设置在登录页面上向用户显示的显示名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-275">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="31a85-276">| | `AllowSynchronousIO`           | `false` | `HttpContext.Request` 和 `HttpContext.Response` 是否允许同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="31a85-276">| | `AllowSynchronousIO`           | `false` | Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> <span data-ttu-id="31a85-277">| | `MaxRequestBodySize`           | `30000000`  | 获取或设置 `HttpRequest` 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="31a85-277">| | `MaxRequestBodySize`           | `30000000`  | Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="31a85-278">请注意，IIS 本身有限制 `maxAllowedContentLength`，这一限制将在 `IISServerOptions` 中设置 `MaxRequestBodySize` 之前进行处理。</span><span class="sxs-lookup"><span data-stu-id="31a85-278">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="31a85-279">更改 `MaxRequestBodySize` 不会影响 `maxAllowedContentLength`。</span><span class="sxs-lookup"><span data-stu-id="31a85-279">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="31a85-280">若要增加 `maxAllowedContentLength`，请在 web.config 中添加一个条目，将 `maxAllowedContentLength` 设置为更高值。</span><span class="sxs-lookup"><span data-stu-id="31a85-280">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="31a85-281">有关更多详细信息，请参阅[配置](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。</span><span class="sxs-lookup"><span data-stu-id="31a85-281">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="31a85-282">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="31a85-282">**Out-of-process hosting model**</span></span>

<span data-ttu-id="31a85-283">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-283">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="31a85-284">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="31a85-284">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="31a85-285">选项</span><span class="sxs-lookup"><span data-stu-id="31a85-285">Option</span></span>                         | <span data-ttu-id="31a85-286">默认</span><span class="sxs-lookup"><span data-stu-id="31a85-286">Default</span></span> | <span data-ttu-id="31a85-287">设置</span><span class="sxs-lookup"><span data-stu-id="31a85-287">Setting</span></span> |
| ---
<span data-ttu-id="31a85-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-289">'Blazor'</span></span>
- <span data-ttu-id="31a85-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-290">'Identity'</span></span>
- <span data-ttu-id="31a85-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-292">'Razor'</span></span>
- <span data-ttu-id="31a85-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-295">'Blazor'</span></span>
- <span data-ttu-id="31a85-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-296">'Identity'</span></span>
- <span data-ttu-id="31a85-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-298">'Razor'</span></span>
- <span data-ttu-id="31a85-299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-301">'Blazor'</span></span>
- <span data-ttu-id="31a85-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-302">'Identity'</span></span>
- <span data-ttu-id="31a85-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-304">'Razor'</span></span>
- <span data-ttu-id="31a85-305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-307">'Blazor'</span></span>
- <span data-ttu-id="31a85-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-308">'Identity'</span></span>
- <span data-ttu-id="31a85-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-310">'Razor'</span></span>
- <span data-ttu-id="31a85-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-313">'Blazor'</span></span>
- <span data-ttu-id="31a85-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-314">'Identity'</span></span>
- <span data-ttu-id="31a85-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-316">'Razor'</span></span>
- <span data-ttu-id="31a85-317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-319">'Blazor'</span></span>
- <span data-ttu-id="31a85-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-320">'Identity'</span></span>
- <span data-ttu-id="31a85-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-322">'Razor'</span></span>
- <span data-ttu-id="31a85-323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-325">'Blazor'</span></span>
- <span data-ttu-id="31a85-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-326">'Identity'</span></span>
- <span data-ttu-id="31a85-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-328">'Razor'</span></span>
- <span data-ttu-id="31a85-329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-331">'Blazor'</span></span>
- <span data-ttu-id="31a85-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-332">'Identity'</span></span>
- <span data-ttu-id="31a85-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-334">'Razor'</span></span>
- <span data-ttu-id="31a85-335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-337">'Blazor'</span></span>
- <span data-ttu-id="31a85-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-338">'Identity'</span></span>
- <span data-ttu-id="31a85-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-340">'Razor'</span></span>
- <span data-ttu-id="31a85-341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-343">'Blazor'</span></span>
- <span data-ttu-id="31a85-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-344">'Identity'</span></span>
- <span data-ttu-id="31a85-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-346">'Razor'</span></span>
- <span data-ttu-id="31a85-347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-349">'Blazor'</span></span>
- <span data-ttu-id="31a85-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-350">'Identity'</span></span>
- <span data-ttu-id="31a85-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-352">'Razor'</span></span>
- <span data-ttu-id="31a85-353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-355">'Blazor'</span></span>
- <span data-ttu-id="31a85-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-356">'Identity'</span></span>
- <span data-ttu-id="31a85-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-358">'Razor'</span></span>
- <span data-ttu-id="31a85-359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-361">'Blazor'</span></span>
- <span data-ttu-id="31a85-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-362">'Identity'</span></span>
- <span data-ttu-id="31a85-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-364">'Razor'</span></span>
- <span data-ttu-id="31a85-365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-365">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-367">'Blazor'</span></span>
- <span data-ttu-id="31a85-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-368">'Identity'</span></span>
- <span data-ttu-id="31a85-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-370">'Razor'</span></span>
- <span data-ttu-id="31a85-371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-371">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-372">---- | | `AutomaticAuthentication`      | `true`  | 如果为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)会将 `HttpContext.User` 设置为进行 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-372">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="31a85-373">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="31a85-373">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="31a85-374">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-374">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="31a85-375">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="31a85-375">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="31a85-376">| | `AuthenticationDisplayName`    | `null`  | 设置在登录页面上向用户显示的显示名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-376">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="31a85-377">| | `ForwardClientCertificate`     | `true`  | 如果为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求标头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="31a85-377">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="31a85-378">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="31a85-378">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="31a85-379">将 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块配置为转发：</span><span class="sxs-lookup"><span data-stu-id="31a85-379">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="31a85-380">方案 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="31a85-380">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="31a85-381">发起请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="31a85-381">Remote IP address where the request originated.</span></span>

<span data-ttu-id="31a85-382">[IIS 集成中间件](#enable-the-iisintegration-components)配置转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="31a85-382">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="31a85-383">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-383">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="31a85-384">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="31a85-384">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="31a85-385">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="31a85-385">web.config file</span></span>

<span data-ttu-id="31a85-386">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="31a85-386">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="31a85-387">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-387">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="31a85-388">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="31a85-388">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="31a85-389">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="31a85-389">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="31a85-390">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)  。</span><span class="sxs-lookup"><span data-stu-id="31a85-390">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="31a85-391">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="31a85-391">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="31a85-392">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="31a85-392">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="31a85-393">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="31a85-393">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="31a85-394">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="31a85-394">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="31a85-395">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="31a85-395">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="31a85-396">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="31a85-396">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="31a85-397">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-397">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="31a85-398">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="31a85-398">web.config file location</span></span>

<span data-ttu-id="31a85-399">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config 文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="31a85-399">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="31a85-400">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="31a85-400">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="31a85-401">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-401">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="31a85-402">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="31a85-402">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="31a85-403">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="31a85-403">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="31a85-404">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-404">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="31a85-405">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="31a85-405">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="31a85-406">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="31a85-406">Transform web.config</span></span>

<span data-ttu-id="31a85-407">如果需要在发布时转换 web.config，请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="31a85-407">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="31a85-408">你可能需要在发布时转换 web.config，以便基于配置、配置文件或环境设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="31a85-408">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="31a85-409">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="31a85-409">IIS configuration</span></span>

<span data-ttu-id="31a85-410">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="31a85-410">**Windows Server operating systems**</span></span>

<span data-ttu-id="31a85-411">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="31a85-411">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="31a85-412">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="31a85-412">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="31a85-413">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="31a85-413">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="31a85-415">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="31a85-415">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="31a85-416">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="31a85-416">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="31a85-418">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-418">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="31a85-419">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="31a85-419">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="31a85-420">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-420">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="31a85-421">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-421">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="31a85-422">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-422">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="31a85-423">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-423">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="31a85-424">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="31a85-424">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="31a85-425">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-425">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="31a85-426">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="31a85-426">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="31a85-427">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="31a85-427">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="31a85-428">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="31a85-428">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="31a85-429">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="31a85-429">**Windows desktop operating systems**</span></span>

<span data-ttu-id="31a85-430">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="31a85-430">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="31a85-431">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="31a85-431">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="31a85-432">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-432">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="31a85-433">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-433">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="31a85-434">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="31a85-434">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="31a85-435">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="31a85-435">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="31a85-436">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-436">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="31a85-437">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-437">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="31a85-438">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="31a85-438">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="31a85-439">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-439">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="31a85-440">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-440">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="31a85-441">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-441">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="31a85-442">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-442">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="31a85-443">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="31a85-443">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="31a85-444">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-444">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="31a85-445">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="31a85-445">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="31a85-446">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="31a85-446">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="31a85-448">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="31a85-448">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="31a85-449">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="31a85-449">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="31a85-450">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="31a85-450">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="31a85-451">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-451">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="31a85-452">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="31a85-452">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="31a85-453">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-453">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="31a85-454">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="31a85-454">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="31a85-455">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="31a85-455">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="31a85-456">直接下载（当前版本）</span><span class="sxs-lookup"><span data-stu-id="31a85-456">Direct download (current version)</span></span>

<span data-ttu-id="31a85-457">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="31a85-457">Download the installer using the following link:</span></span>

[<span data-ttu-id="31a85-458">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="31a85-458">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="31a85-459">先前版本的安装程序</span><span class="sxs-lookup"><span data-stu-id="31a85-459">Earlier versions of the installer</span></span>

<span data-ttu-id="31a85-460">若要获取先前版本的安装程序：</span><span class="sxs-lookup"><span data-stu-id="31a85-460">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="31a85-461">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="31a85-461">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="31a85-462">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-462">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="31a85-463">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="31a85-463">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="31a85-464">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-464">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="31a85-465">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-465">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="31a85-466">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="31a85-466">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="31a85-467">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="31a85-467">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="31a85-468">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-468">Run the installer on the server.</span></span> <span data-ttu-id="31a85-469">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="31a85-469">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="31a85-470">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="31a85-470">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="31a85-471">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-471">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="31a85-472">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="31a85-472">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="31a85-473">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="31a85-473">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="31a85-474">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="31a85-474">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="31a85-475">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-475">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="31a85-476">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="31a85-476">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="31a85-477">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-477">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="31a85-478">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (applicationHost.config) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="31a85-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="31a85-479">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-479">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="31a85-480">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="31a85-480">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="31a85-481">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="31a85-481">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="31a85-482">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="31a85-482">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="31a85-483">ASP.NET Core 不采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="31a85-483">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="31a85-484">通过安装新的托管捆绑包升级共享框架后，请重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="31a85-484">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="31a85-485">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="31a85-485">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="31a85-486">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="31a85-486">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="31a85-487">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="31a85-487">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="31a85-488">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-488">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="31a85-489">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="31a85-489">The preferred method is to use WebPI.</span></span> <span data-ttu-id="31a85-490">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-490">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="31a85-491">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="31a85-491">Create the IIS site</span></span>

1. <span data-ttu-id="31a85-492">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-492">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="31a85-493">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="31a85-493">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="31a85-494">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="31a85-494">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="31a85-495">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-495">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="31a85-496">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="31a85-496">Right-click the **Sites** folder.</span></span> <span data-ttu-id="31a85-497">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="31a85-497">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="31a85-498">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="31a85-498">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="31a85-499">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="31a85-499">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="31a85-501">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="31a85-501">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="31a85-502">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="31a85-502">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="31a85-503">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="31a85-503">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="31a85-504">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="31a85-504">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="31a85-505">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="31a85-505">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="31a85-506">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="31a85-506">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="31a85-507">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="31a85-507">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="31a85-508">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="31a85-508">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="31a85-509">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="31a85-509">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="31a85-511">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-511">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="31a85-512">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载。</span><span class="sxs-lookup"><span data-stu-id="31a85-512">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="31a85-513">将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR)，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-513">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="31a85-514">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="31a85-514">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="31a85-515">*ASP.NET Core 2.2 或更高版本*：</span><span class="sxs-lookup"><span data-stu-id="31a85-515">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="31a85-516">对于使用 32 位 SDK 发布的 32 位 (x86) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，且该 SDK 使用[进程内托管模型](#in-process-hosting-model)，请为 32 位启用应用程序池。</span><span class="sxs-lookup"><span data-stu-id="31a85-516">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="31a85-517">在 IIS 管理器中，导航到“连接”边栏中的“应用程序池” 。</span><span class="sxs-lookup"><span data-stu-id="31a85-517">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="31a85-518">选择应用的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="31a85-518">Select the app's Application Pool.</span></span> <span data-ttu-id="31a85-519">在“操作”边栏中，选择，“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="31a85-519">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="31a85-520">将“启用 32 位应用程序”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="31a85-520">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="31a85-521">对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-521">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="31a85-522">在 IIS 管理器中，导航到“连接”边栏中的“应用程序池” 。</span><span class="sxs-lookup"><span data-stu-id="31a85-522">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="31a85-523">选择应用的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="31a85-523">Select the app's Application Pool.</span></span> <span data-ttu-id="31a85-524">在“操作”边栏中，选择，“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="31a85-524">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="31a85-525">将“启用 32 位应用程序”设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="31a85-525">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="31a85-526">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-526">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="31a85-527">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="31a85-527">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="31a85-528">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-528">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="31a85-529">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-529">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="31a85-530">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-530">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="31a85-531">部署应用</span><span class="sxs-lookup"><span data-stu-id="31a85-531">Deploy the app</span></span>

<span data-ttu-id="31a85-532">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="31a85-532">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="31a85-533">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="31a85-533">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="31a85-534">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="31a85-534">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="31a85-535">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="31a85-535">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="31a85-536">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="31a85-536">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="31a85-538">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="31a85-538">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="31a85-539">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="31a85-539">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="31a85-540">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="31a85-540">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="31a85-541">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="31a85-541">Alternatives to Web Deploy</span></span>

<span data-ttu-id="31a85-542">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="31a85-542">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="31a85-543">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="31a85-543">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="31a85-544">浏览网站</span><span class="sxs-lookup"><span data-stu-id="31a85-544">Browse the website</span></span>

<span data-ttu-id="31a85-545">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-545">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="31a85-546">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="31a85-546">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="31a85-547">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="31a85-547">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="31a85-549">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="31a85-549">Locked deployment files</span></span>

<span data-ttu-id="31a85-550">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="31a85-550">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="31a85-551">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-551">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="31a85-552">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="31a85-552">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="31a85-553">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="31a85-553">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="31a85-554">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-554">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="31a85-555">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-555">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="31a85-556">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="31a85-556">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="31a85-557">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-557">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="31a85-558">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="31a85-558">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="31a85-559">数据保护</span><span class="sxs-lookup"><span data-stu-id="31a85-559">Data protection</span></span>

<span data-ttu-id="31a85-560">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="31a85-560">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="31a85-561">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="31a85-561">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="31a85-562">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="31a85-562">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="31a85-563">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="31a85-563">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="31a85-564">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="31a85-564">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="31a85-565">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="31a85-565">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="31a85-566">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="31a85-566">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="31a85-567">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="31a85-567">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="31a85-568">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="31a85-568">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="31a85-569">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="31a85-569">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="31a85-570">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="31a85-570">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="31a85-571">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="31a85-571">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="31a85-572">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="31a85-572">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="31a85-573">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="31a85-573">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="31a85-574">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="31a85-574">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="31a85-575">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="31a85-575">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="31a85-576">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="31a85-576">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="31a85-577">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="31a85-577">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="31a85-578">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="31a85-578">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="31a85-579">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="31a85-579">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="31a85-580">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="31a85-580">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="31a85-581">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="31a85-581">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="31a85-582">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="31a85-582">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="31a85-583">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="31a85-583">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="31a85-584">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="31a85-584">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="31a85-585">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="31a85-585">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="31a85-586">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="31a85-586">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="31a85-587">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="31a85-587">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="31a85-588">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="31a85-588">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="31a85-589">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="31a85-589">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="31a85-590">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="31a85-590">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="31a85-591">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-591">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="31a85-592">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="31a85-592">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="31a85-593">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="31a85-593">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="31a85-594">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="31a85-594">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="31a85-595">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="31a85-595">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="31a85-596">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="31a85-596">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="31a85-597">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="31a85-597">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="31a85-598">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="31a85-598">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="31a85-599">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="31a85-599">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="31a85-600">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="31a85-600">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="31a85-601">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="31a85-601">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="31a85-602">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="31a85-602">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="31a85-603">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="31a85-603">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="31a85-604">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="31a85-604">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="31a85-605">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="31a85-605">Virtual Directories</span></span>

<span data-ttu-id="31a85-606">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="31a85-606">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="31a85-607">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="31a85-607">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="31a85-608">子应用程序</span><span class="sxs-lookup"><span data-stu-id="31a85-608">Sub-applications</span></span>

<span data-ttu-id="31a85-609">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="31a85-609">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="31a85-610">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="31a85-610">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="31a85-611">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="31a85-611">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="31a85-612">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="31a85-612">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="31a85-613">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="31a85-613">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="31a85-614">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-614">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="31a85-615">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="31a85-615">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="31a85-616">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="31a85-616">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="31a85-617">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="31a85-617">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="31a85-618">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="31a85-618">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="31a85-619">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-619">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="31a85-620">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="31a85-620">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="31a85-621">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="31a85-621">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="31a85-622">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="31a85-622">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="31a85-623">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="31a85-623">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="31a85-624">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-624">Select **OK**.</span></span>

<span data-ttu-id="31a85-625">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-625">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="31a85-626">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-626">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="31a85-627">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="31a85-627">Configuration of IIS with web.config</span></span>

<span data-ttu-id="31a85-628">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="31a85-628">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="31a85-629">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="31a85-629">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="31a85-630">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="31a85-630">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="31a85-631">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="31a85-631">For more information, see the following topics:</span></span>

* [<span data-ttu-id="31a85-632">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="31a85-632">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="31a85-633">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="31a85-633">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="31a85-634">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="31a85-634">Configuration sections of web.config</span></span>

<span data-ttu-id="31a85-635">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="31a85-635">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="31a85-636">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-636">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="31a85-637">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="31a85-637">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="31a85-638">应用程序池</span><span class="sxs-lookup"><span data-stu-id="31a85-638">Application Pools</span></span>

<span data-ttu-id="31a85-639">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="31a85-639">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="31a85-640">托管在进程内：应用需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-640">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="31a85-641">托管在进程外：建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="31a85-641">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="31a85-642">IIS“添加网站”对话框默认为每应用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-642">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="31a85-643">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="31a85-643">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="31a85-644">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-644">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="31a85-645">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="31a85-645">Application Pool Identity</span></span>

<span data-ttu-id="31a85-646">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="31a85-646">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="31a85-647">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="31a85-647">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="31a85-648">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="31a85-648">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="31a85-650">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="31a85-650">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="31a85-651">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="31a85-651">Resources can be secured using this identity.</span></span> <span data-ttu-id="31a85-652">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="31a85-652">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="31a85-653">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="31a85-653">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="31a85-654">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="31a85-654">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="31a85-655">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="31a85-655">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="31a85-656">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="31a85-656">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="31a85-657">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="31a85-657">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="31a85-658">在“输入要选择的对象名称”区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="31a85-658">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="31a85-659">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="31a85-659">Select the **Check Names** button.</span></span> <span data-ttu-id="31a85-660">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-660">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="31a85-661">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="31a85-661">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="31a85-662">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-662">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="31a85-663">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="31a85-663">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="31a85-665">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-665">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="31a85-667">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-667">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="31a85-668">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-668">Provide additional permissions as needed.</span></span>

<span data-ttu-id="31a85-669">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-669">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="31a85-670">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="31a85-670">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="31a85-671">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="31a85-671">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="31a85-672">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="31a85-672">HTTP/2 support</span></span>

<span data-ttu-id="31a85-673">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="31a85-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="31a85-674">进程内</span><span class="sxs-lookup"><span data-stu-id="31a85-674">In-process</span></span>
  * <span data-ttu-id="31a85-675">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-675">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="31a85-676">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="31a85-676">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="31a85-677">进程外</span><span class="sxs-lookup"><span data-stu-id="31a85-677">Out-of-process</span></span>
  * <span data-ttu-id="31a85-678">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-678">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="31a85-679">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="31a85-679">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="31a85-680">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="31a85-680">TLS 1.2 or later connection</span></span>

<span data-ttu-id="31a85-681">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="31a85-681">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="31a85-682">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="31a85-682">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="31a85-683">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-683">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="31a85-684">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="31a85-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="31a85-685">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="31a85-685">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="31a85-686">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="31a85-686">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="31a85-687">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="31a85-687">CORS preflight requests</span></span>

<span data-ttu-id="31a85-688">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-688">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="31a85-689">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-689">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="31a85-690">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="31a85-690">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="31a85-691">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="31a85-691">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="31a85-692">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="31a85-692">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="31a85-693">[应用程序初始化模块](#application-initialization-module)：可以将托管在[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)的应用配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="31a85-693">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="31a85-694">[空闲超时](#idle-timeout)：可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段不超时。</span><span class="sxs-lookup"><span data-stu-id="31a85-694">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="31a85-695">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="31a85-695">Application Initialization Module</span></span>

<span data-ttu-id="31a85-696">适用于托管在进程内和进程外的应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-696">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="31a85-697">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-697">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="31a85-698">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="31a85-698">The request triggers the app to start.</span></span> <span data-ttu-id="31a85-699">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="31a85-699">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="31a85-700">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="31a85-700">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="31a85-701">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="31a85-701">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="31a85-702">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="31a85-702">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="31a85-703">打开“Internet Information Services”>“万维网服务”>“应用程序开发功能”。</span><span class="sxs-lookup"><span data-stu-id="31a85-703">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="31a85-704">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="31a85-704">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="31a85-705">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="31a85-705">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="31a85-706">打开“添加角色和功能向导”。</span><span class="sxs-lookup"><span data-stu-id="31a85-706">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="31a85-707">在“选择角色服务”面板中，打开“应用程序开发”节点 。</span><span class="sxs-lookup"><span data-stu-id="31a85-707">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="31a85-708">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="31a85-708">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="31a85-709">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="31a85-709">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="31a85-710">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="31a85-710">Using IIS Manager:</span></span>

  1. <span data-ttu-id="31a85-711">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="31a85-711">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="31a85-712">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="31a85-712">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="31a85-713">默认的“启动模式”为“按需” 。</span><span class="sxs-lookup"><span data-stu-id="31a85-713">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="31a85-714">将“启动模式”设置为“始终运行”。</span><span class="sxs-lookup"><span data-stu-id="31a85-714">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="31a85-715">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-715">Select **OK**.</span></span>
  1. <span data-ttu-id="31a85-716">打开“连接”面板中的“网站”节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-716">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="31a85-717">右键单击应用，并选择“管理网站”>“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="31a85-717">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="31a85-718">默认的“预加载已启用”设置为“False” 。</span><span class="sxs-lookup"><span data-stu-id="31a85-718">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="31a85-719">将“预加载已启用”设置为“True”。</span><span class="sxs-lookup"><span data-stu-id="31a85-719">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="31a85-720">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-720">Select **OK**.</span></span>

* <span data-ttu-id="31a85-721">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中 ：</span><span class="sxs-lookup"><span data-stu-id="31a85-721">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="31a85-722">空闲超时</span><span class="sxs-lookup"><span data-stu-id="31a85-722">Idle Timeout</span></span>

<span data-ttu-id="31a85-723">仅适用于托管在进程内的应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-723">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="31a85-724">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="31a85-724">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="31a85-725">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="31a85-725">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="31a85-726">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="31a85-726">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="31a85-727">默认的“空闲超时(分钟)”为“20”分钟 。</span><span class="sxs-lookup"><span data-stu-id="31a85-727">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="31a85-728">将“空闲超时(分钟)”设置为“0”（零） 。</span><span class="sxs-lookup"><span data-stu-id="31a85-728">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="31a85-729">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-729">Select **OK**.</span></span>
1. <span data-ttu-id="31a85-730">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="31a85-730">Recycle the worker process.</span></span>

<span data-ttu-id="31a85-731">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="31a85-731">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="31a85-732">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="31a85-732">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="31a85-733">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="31a85-733">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="31a85-734">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="31a85-734">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="31a85-735">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="31a85-735">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="31a85-736">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="31a85-736">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="31a85-737">[应用程序池 \<processModel> 的进程模型设置](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="31a85-737">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="31a85-738">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="31a85-738">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="31a85-739">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="31a85-739">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="31a85-740">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="31a85-740">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="31a85-741">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="31a85-741">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="31a85-742">其他资源</span><span class="sxs-lookup"><span data-stu-id="31a85-742">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="31a85-743">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="31a85-743">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="31a85-744">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="31a85-744">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="31a85-745">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="31a85-745">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="31a85-746">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="31a85-746">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="31a85-747">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="31a85-747">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="31a85-748">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="31a85-748">Supported operating systems</span></span>

<span data-ttu-id="31a85-749">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="31a85-749">The following operating systems are supported:</span></span>

* <span data-ttu-id="31a85-750">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-750">Windows 7 or later</span></span>
* <span data-ttu-id="31a85-751">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-751">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="31a85-752">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="31a85-752">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="31a85-753">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="31a85-753">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="31a85-754">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="31a85-754">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="31a85-755">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="31a85-755">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="31a85-756">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="31a85-756">Supported platforms</span></span>

<span data-ttu-id="31a85-757">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-757">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="31a85-758">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="31a85-758">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="31a85-759">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="31a85-759">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="31a85-760">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="31a85-760">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="31a85-761">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="31a85-761">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="31a85-762">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-762">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="31a85-763">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-763">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="31a85-764">托管模型</span><span class="sxs-lookup"><span data-stu-id="31a85-764">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="31a85-765">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="31a85-765">In-process hosting model</span></span>

<span data-ttu-id="31a85-766">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-766">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="31a85-767">进程内托管的性能高于进程外托管的性能，因为：</span><span class="sxs-lookup"><span data-stu-id="31a85-767">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="31a85-768">请求并不通过环回适配器进行代理。</span><span class="sxs-lookup"><span data-stu-id="31a85-768">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="31a85-769">环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="31a85-769">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="31a85-770">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="31a85-770">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="31a85-771">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="31a85-771">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="31a85-772">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="31a85-772">Performs app initialization.</span></span>
  * <span data-ttu-id="31a85-773">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="31a85-773">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="31a85-774">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="31a85-774">Calls `Program.Main`.</span></span>
* <span data-ttu-id="31a85-775">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="31a85-775">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="31a85-776">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="31a85-776">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="31a85-777">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="31a85-777">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

<span data-ttu-id="31a85-779">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-779">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="31a85-780">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="31a85-780">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="31a85-781">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="31a85-781">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="31a85-782">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="31a85-782">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="31a85-783">IIS HTTP 服务器处理请求之后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="31a85-783">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="31a85-784">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="31a85-784">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="31a85-785">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="31a85-785">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="31a85-786">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="31a85-786">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="31a85-787">进程内托管选择使用现有应用，但 [dotnet new](/dotnet/core/tools/dotnet-new) 模板默认使用所有 IIS 和 IIS Express 方案的进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="31a85-787">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="31a85-788">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。 </span><span class="sxs-lookup"><span data-stu-id="31a85-788">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="31a85-789">性能测试表明，与在进程外托管应用并将请求代理到 [Kestrel](xref:fundamentals/servers/kestrel) 服务器相比，在进程中托管 .NET Core 应用可以大大提升请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="31a85-789">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="31a85-790">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="31a85-790">Out-of-process hosting model</span></span>

<span data-ttu-id="31a85-791">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="31a85-791">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="31a85-792">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-792">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="31a85-793">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="31a85-793">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="31a85-794">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="31a85-794">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="31a85-796">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-796">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="31a85-797">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="31a85-797">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="31a85-798">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="31a85-798">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="31a85-799">该模块在启动时通过环境变量指定端口，<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="31a85-799">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="31a85-800">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-800">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="31a85-801">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="31a85-801">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="31a85-802">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="31a85-802">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="31a85-803">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="31a85-803">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="31a85-804">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="31a85-804">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="31a85-805">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="31a85-805">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="31a85-806">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-806">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="31a85-807">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="31a85-807">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="31a85-808">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="31a85-808">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="31a85-809">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="31a85-809">Enable the IISIntegration components</span></span>

<span data-ttu-id="31a85-810">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="31a85-810">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="31a85-811">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="31a85-811">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="31a85-812">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="31a85-812">IIS options</span></span>

<span data-ttu-id="31a85-813">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="31a85-813">**In-process hosting model**</span></span>

<span data-ttu-id="31a85-814">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-814">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="31a85-815">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="31a85-815">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="31a85-816">选项</span><span class="sxs-lookup"><span data-stu-id="31a85-816">Option</span></span>                         | <span data-ttu-id="31a85-817">默认</span><span class="sxs-lookup"><span data-stu-id="31a85-817">Default</span></span> | <span data-ttu-id="31a85-818">设置</span><span class="sxs-lookup"><span data-stu-id="31a85-818">Setting</span></span> |
| ---
<span data-ttu-id="31a85-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-820">'Blazor'</span></span>
- <span data-ttu-id="31a85-821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-821">'Identity'</span></span>
- <span data-ttu-id="31a85-822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-822">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-823">'Razor'</span></span>
- <span data-ttu-id="31a85-824">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-826">'Blazor'</span></span>
- <span data-ttu-id="31a85-827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-827">'Identity'</span></span>
- <span data-ttu-id="31a85-828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-828">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-829">'Razor'</span></span>
- <span data-ttu-id="31a85-830">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-832">'Blazor'</span></span>
- <span data-ttu-id="31a85-833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-833">'Identity'</span></span>
- <span data-ttu-id="31a85-834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-834">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-835">'Razor'</span></span>
- <span data-ttu-id="31a85-836">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-838">'Blazor'</span></span>
- <span data-ttu-id="31a85-839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-839">'Identity'</span></span>
- <span data-ttu-id="31a85-840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-840">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-841">'Razor'</span></span>
- <span data-ttu-id="31a85-842">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-844">'Blazor'</span></span>
- <span data-ttu-id="31a85-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-845">'Identity'</span></span>
- <span data-ttu-id="31a85-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-847">'Razor'</span></span>
- <span data-ttu-id="31a85-848">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-850">'Blazor'</span></span>
- <span data-ttu-id="31a85-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-851">'Identity'</span></span>
- <span data-ttu-id="31a85-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-853">'Razor'</span></span>
- <span data-ttu-id="31a85-854">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-856">'Blazor'</span></span>
- <span data-ttu-id="31a85-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-857">'Identity'</span></span>
- <span data-ttu-id="31a85-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-859">'Razor'</span></span>
- <span data-ttu-id="31a85-860">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-862">'Blazor'</span></span>
- <span data-ttu-id="31a85-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-863">'Identity'</span></span>
- <span data-ttu-id="31a85-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-865">'Razor'</span></span>
- <span data-ttu-id="31a85-866">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-868">'Blazor'</span></span>
- <span data-ttu-id="31a85-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-869">'Identity'</span></span>
- <span data-ttu-id="31a85-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-871">'Razor'</span></span>
- <span data-ttu-id="31a85-872">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-874">'Blazor'</span></span>
- <span data-ttu-id="31a85-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-875">'Identity'</span></span>
- <span data-ttu-id="31a85-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-877">'Razor'</span></span>
- <span data-ttu-id="31a85-878">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-880">'Blazor'</span></span>
- <span data-ttu-id="31a85-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-881">'Identity'</span></span>
- <span data-ttu-id="31a85-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-883">'Razor'</span></span>
- <span data-ttu-id="31a85-884">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-886">'Blazor'</span></span>
- <span data-ttu-id="31a85-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-887">'Identity'</span></span>
- <span data-ttu-id="31a85-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-889">'Razor'</span></span>
- <span data-ttu-id="31a85-890">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-892">'Blazor'</span></span>
- <span data-ttu-id="31a85-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-893">'Identity'</span></span>
- <span data-ttu-id="31a85-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-895">'Razor'</span></span>
- <span data-ttu-id="31a85-896">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-896">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-898">'Blazor'</span></span>
- <span data-ttu-id="31a85-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-899">'Identity'</span></span>
- <span data-ttu-id="31a85-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-901">'Razor'</span></span>
- <span data-ttu-id="31a85-902">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-902">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-903">---- | | `AutomaticAuthentication`      | `true`  | 如果为 `true`，IIS 服务器会将 `HttpContext.User` 设置为进行 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-903">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="31a85-904">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="31a85-904">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="31a85-905">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-905">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="31a85-906">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-906">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="31a85-907">| | `AuthenticationDisplayName`    | `null`  | 设置在登录页面上向用户显示的显示名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-907">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="31a85-908">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="31a85-908">**Out-of-process hosting model**</span></span>

<span data-ttu-id="31a85-909">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-909">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="31a85-910">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="31a85-910">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="31a85-911">选项</span><span class="sxs-lookup"><span data-stu-id="31a85-911">Option</span></span>                         | <span data-ttu-id="31a85-912">默认</span><span class="sxs-lookup"><span data-stu-id="31a85-912">Default</span></span> | <span data-ttu-id="31a85-913">设置</span><span class="sxs-lookup"><span data-stu-id="31a85-913">Setting</span></span> |
| ---
<span data-ttu-id="31a85-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-915">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-915">'Blazor'</span></span>
- <span data-ttu-id="31a85-916">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-916">'Identity'</span></span>
- <span data-ttu-id="31a85-917">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-917">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-918">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-918">'Razor'</span></span>
- <span data-ttu-id="31a85-919">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-919">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-921">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-921">'Blazor'</span></span>
- <span data-ttu-id="31a85-922">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-922">'Identity'</span></span>
- <span data-ttu-id="31a85-923">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-923">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-924">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-924">'Razor'</span></span>
- <span data-ttu-id="31a85-925">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-925">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-927">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-927">'Blazor'</span></span>
- <span data-ttu-id="31a85-928">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-928">'Identity'</span></span>
- <span data-ttu-id="31a85-929">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-929">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-930">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-930">'Razor'</span></span>
- <span data-ttu-id="31a85-931">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-931">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-933">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-933">'Blazor'</span></span>
- <span data-ttu-id="31a85-934">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-934">'Identity'</span></span>
- <span data-ttu-id="31a85-935">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-935">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-936">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-936">'Razor'</span></span>
- <span data-ttu-id="31a85-937">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-937">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-939">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-939">'Blazor'</span></span>
- <span data-ttu-id="31a85-940">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-940">'Identity'</span></span>
- <span data-ttu-id="31a85-941">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-941">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-942">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-942">'Razor'</span></span>
- <span data-ttu-id="31a85-943">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-943">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-945">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-945">'Blazor'</span></span>
- <span data-ttu-id="31a85-946">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-946">'Identity'</span></span>
- <span data-ttu-id="31a85-947">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-947">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-948">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-948">'Razor'</span></span>
- <span data-ttu-id="31a85-949">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-949">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-951">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-951">'Blazor'</span></span>
- <span data-ttu-id="31a85-952">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-952">'Identity'</span></span>
- <span data-ttu-id="31a85-953">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-953">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-954">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-954">'Razor'</span></span>
- <span data-ttu-id="31a85-955">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-955">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-957">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-957">'Blazor'</span></span>
- <span data-ttu-id="31a85-958">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-958">'Identity'</span></span>
- <span data-ttu-id="31a85-959">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-959">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-960">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-960">'Razor'</span></span>
- <span data-ttu-id="31a85-961">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-961">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-963">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-963">'Blazor'</span></span>
- <span data-ttu-id="31a85-964">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-964">'Identity'</span></span>
- <span data-ttu-id="31a85-965">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-965">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-966">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-966">'Razor'</span></span>
- <span data-ttu-id="31a85-967">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-967">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-969">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-969">'Blazor'</span></span>
- <span data-ttu-id="31a85-970">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-970">'Identity'</span></span>
- <span data-ttu-id="31a85-971">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-971">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-972">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-972">'Razor'</span></span>
- <span data-ttu-id="31a85-973">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-973">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-975">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-975">'Blazor'</span></span>
- <span data-ttu-id="31a85-976">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-976">'Identity'</span></span>
- <span data-ttu-id="31a85-977">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-977">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-978">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-978">'Razor'</span></span>
- <span data-ttu-id="31a85-979">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-979">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-981">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-981">'Blazor'</span></span>
- <span data-ttu-id="31a85-982">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-982">'Identity'</span></span>
- <span data-ttu-id="31a85-983">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-983">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-984">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-984">'Razor'</span></span>
- <span data-ttu-id="31a85-985">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-985">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-987">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-987">'Blazor'</span></span>
- <span data-ttu-id="31a85-988">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-988">'Identity'</span></span>
- <span data-ttu-id="31a85-989">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-989">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-990">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-990">'Razor'</span></span>
- <span data-ttu-id="31a85-991">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-991">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-993">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-993">'Blazor'</span></span>
- <span data-ttu-id="31a85-994">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-994">'Identity'</span></span>
- <span data-ttu-id="31a85-995">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-995">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-996">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-996">'Razor'</span></span>
- <span data-ttu-id="31a85-997">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-997">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-998">---- | | `AutomaticAuthentication`      | `true`  | 如果为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)会将 `HttpContext.User` 设置为进行 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-998">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="31a85-999">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="31a85-999">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="31a85-1000">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-1000">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="31a85-1001">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="31a85-1001">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="31a85-1002">| | `AuthenticationDisplayName`    | `null`  | 设置在登录页面上向用户显示的显示名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-1002">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="31a85-1003">| | `ForwardClientCertificate`     | `true`  | 如果为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求标头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1003">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="31a85-1004">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="31a85-1004">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="31a85-1005">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="31a85-1005">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="31a85-1006">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-1006">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="31a85-1007">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1007">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="31a85-1008">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="31a85-1008">web.config file</span></span>

<span data-ttu-id="31a85-1009">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1009">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="31a85-1010">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1010">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="31a85-1011">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1011">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="31a85-1012">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="31a85-1012">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="31a85-1013">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)  。</span><span class="sxs-lookup"><span data-stu-id="31a85-1013">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="31a85-1014">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="31a85-1014">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="31a85-1015">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="31a85-1015">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="31a85-1016">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="31a85-1016">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="31a85-1017">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="31a85-1017">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="31a85-1018">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="31a85-1018">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="31a85-1019">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="31a85-1019">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="31a85-1020">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1020">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="31a85-1021">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="31a85-1021">web.config file location</span></span>

<span data-ttu-id="31a85-1022">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config 文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1022">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="31a85-1023">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="31a85-1023">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="31a85-1024">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1024">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="31a85-1025">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="31a85-1025">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="31a85-1026">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="31a85-1026">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="31a85-1027">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1027">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="31a85-1028">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="31a85-1028">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="31a85-1029">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="31a85-1029">Transform web.config</span></span>

<span data-ttu-id="31a85-1030">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1030">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="31a85-1031">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="31a85-1031">IIS configuration</span></span>

<span data-ttu-id="31a85-1032">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="31a85-1032">**Windows Server operating systems**</span></span>

<span data-ttu-id="31a85-1033">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="31a85-1033">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="31a85-1034">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="31a85-1034">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="31a85-1035">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1035">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="31a85-1037">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="31a85-1037">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="31a85-1038">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="31a85-1038">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="31a85-1040">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-1040">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="31a85-1041">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1041">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="31a85-1042">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-1042">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="31a85-1043">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1043">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="31a85-1044">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-1044">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="31a85-1045">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-1045">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="31a85-1046">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1046">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="31a85-1047">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-1047">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="31a85-1048">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1048">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="31a85-1049">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="31a85-1049">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="31a85-1050">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="31a85-1050">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="31a85-1051">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="31a85-1051">**Windows desktop operating systems**</span></span>

<span data-ttu-id="31a85-1052">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1052">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="31a85-1053">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="31a85-1053">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="31a85-1054">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-1054">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="31a85-1055">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-1055">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="31a85-1056">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="31a85-1056">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="31a85-1057">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="31a85-1057">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="31a85-1058">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-1058">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="31a85-1059">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-1059">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="31a85-1060">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1060">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="31a85-1061">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-1061">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="31a85-1062">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1062">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="31a85-1063">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-1063">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="31a85-1064">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-1064">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="31a85-1065">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1065">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="31a85-1066">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-1066">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="31a85-1067">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1067">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="31a85-1068">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="31a85-1068">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="31a85-1070">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="31a85-1070">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="31a85-1071">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="31a85-1071">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="31a85-1072">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1072">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="31a85-1073">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-1073">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="31a85-1074">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="31a85-1074">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="31a85-1075">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1075">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="31a85-1076">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="31a85-1076">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="31a85-1077">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1077">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="31a85-1078">下载</span><span class="sxs-lookup"><span data-stu-id="31a85-1078">Download</span></span>

1. <span data-ttu-id="31a85-1079">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="31a85-1079">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="31a85-1080">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-1080">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="31a85-1081">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="31a85-1081">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="31a85-1082">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1082">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="31a85-1083">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-1083">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="31a85-1084">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1084">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="31a85-1085">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="31a85-1085">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="31a85-1086">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1086">Run the installer on the server.</span></span> <span data-ttu-id="31a85-1087">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="31a85-1087">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="31a85-1088">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="31a85-1088">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="31a85-1089">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-1089">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="31a85-1090">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1090">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="31a85-1091">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="31a85-1091">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="31a85-1092">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1092">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="31a85-1093">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-1093">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="31a85-1094">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="31a85-1094">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="31a85-1095">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-1095">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="31a85-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (applicationHost.config) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="31a85-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="31a85-1097">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1097">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="31a85-1098">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1098">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="31a85-1099">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="31a85-1099">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="31a85-1100">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="31a85-1100">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="31a85-1101">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="31a85-1101">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="31a85-1102">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="31a85-1102">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="31a85-1103">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="31a85-1103">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="31a85-1104">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="31a85-1104">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="31a85-1105">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="31a85-1105">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="31a85-1106">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="31a85-1106">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="31a85-1107">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1107">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="31a85-1108">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="31a85-1108">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="31a85-1109">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="31a85-1109">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="31a85-1110">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1110">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="31a85-1111">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="31a85-1111">The preferred method is to use WebPI.</span></span> <span data-ttu-id="31a85-1112">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-1112">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="31a85-1113">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="31a85-1113">Create the IIS site</span></span>

1. <span data-ttu-id="31a85-1114">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1114">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="31a85-1115">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="31a85-1115">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="31a85-1116">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1116">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="31a85-1117">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-1117">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="31a85-1118">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="31a85-1118">Right-click the **Sites** folder.</span></span> <span data-ttu-id="31a85-1119">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1119">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="31a85-1120">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1120">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="31a85-1121">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="31a85-1121">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="31a85-1123">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="31a85-1123">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="31a85-1124">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="31a85-1124">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="31a85-1125">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="31a85-1125">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="31a85-1126">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="31a85-1126">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="31a85-1127">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="31a85-1127">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="31a85-1128">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1128">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="31a85-1129">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1129">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="31a85-1130">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1130">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="31a85-1131">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="31a85-1131">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="31a85-1133">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-1133">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="31a85-1134">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1134">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="31a85-1135">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="31a85-1135">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="31a85-1136">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1136">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="31a85-1137">在 IIS 管理器 >“应用程序池”的“操作”侧栏中，选择“设置应用程序池默认设置”或“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1137">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="31a85-1138">找到“启用 32 位应用程序”并将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1138">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="31a85-1139">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1139">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="31a85-1140">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-1140">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="31a85-1141">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="31a85-1141">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="31a85-1142">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1142">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="31a85-1143">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-1143">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="31a85-1144">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1144">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="31a85-1145">部署应用</span><span class="sxs-lookup"><span data-stu-id="31a85-1145">Deploy the app</span></span>

<span data-ttu-id="31a85-1146">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="31a85-1146">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="31a85-1147">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="31a85-1147">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="31a85-1148">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="31a85-1148">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="31a85-1149">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1149">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="31a85-1150">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="31a85-1150">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="31a85-1152">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="31a85-1152">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="31a85-1153">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1153">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="31a85-1154">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="31a85-1154">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="31a85-1155">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="31a85-1155">Alternatives to Web Deploy</span></span>

<span data-ttu-id="31a85-1156">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="31a85-1156">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="31a85-1157">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="31a85-1157">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="31a85-1158">浏览网站</span><span class="sxs-lookup"><span data-stu-id="31a85-1158">Browse the website</span></span>

<span data-ttu-id="31a85-1159">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-1159">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="31a85-1160">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1160">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="31a85-1161">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="31a85-1161">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="31a85-1163">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="31a85-1163">Locked deployment files</span></span>

<span data-ttu-id="31a85-1164">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="31a85-1164">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="31a85-1165">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1165">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="31a85-1166">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="31a85-1166">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="31a85-1167">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1167">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="31a85-1168">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1168">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="31a85-1169">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1169">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="31a85-1170">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1170">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="31a85-1171">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1171">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="31a85-1172">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="31a85-1172">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="31a85-1173">数据保护</span><span class="sxs-lookup"><span data-stu-id="31a85-1173">Data protection</span></span>

<span data-ttu-id="31a85-1174">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1174">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="31a85-1175">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1175">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="31a85-1176">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="31a85-1176">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="31a85-1177">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="31a85-1177">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="31a85-1178">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="31a85-1178">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="31a85-1179">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="31a85-1179">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="31a85-1180">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="31a85-1180">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="31a85-1181">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1181">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="31a85-1182">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="31a85-1182">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="31a85-1183">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="31a85-1183">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="31a85-1184">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1184">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="31a85-1185">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="31a85-1185">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="31a85-1186">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1186">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="31a85-1187">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="31a85-1187">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="31a85-1188">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="31a85-1188">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="31a85-1189">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="31a85-1189">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="31a85-1190">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="31a85-1190">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="31a85-1191">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="31a85-1191">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="31a85-1192">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="31a85-1192">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="31a85-1193">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1193">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="31a85-1194">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1194">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="31a85-1195">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="31a85-1195">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="31a85-1196">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1196">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="31a85-1197">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1197">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="31a85-1198">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="31a85-1198">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="31a85-1199">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1199">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="31a85-1200">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1200">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="31a85-1201">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1201">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="31a85-1202">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1202">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="31a85-1203">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="31a85-1203">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="31a85-1204">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="31a85-1204">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="31a85-1205">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1205">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="31a85-1206">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="31a85-1206">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="31a85-1207">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1207">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="31a85-1208">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="31a85-1208">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="31a85-1209">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1209">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="31a85-1210">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="31a85-1210">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="31a85-1211">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1211">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="31a85-1212">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="31a85-1212">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="31a85-1213">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="31a85-1213">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="31a85-1214">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="31a85-1214">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="31a85-1215">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1215">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="31a85-1216">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="31a85-1216">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="31a85-1217">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1217">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="31a85-1218">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1218">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="31a85-1219">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="31a85-1219">Virtual Directories</span></span>

<span data-ttu-id="31a85-1220">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1220">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="31a85-1221">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1221">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="31a85-1222">子应用程序</span><span class="sxs-lookup"><span data-stu-id="31a85-1222">Sub-applications</span></span>

<span data-ttu-id="31a85-1223">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1223">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="31a85-1224">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="31a85-1224">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="31a85-1225">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="31a85-1225">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="31a85-1226">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="31a85-1226">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="31a85-1227">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1227">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="31a85-1228">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-1228">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="31a85-1229">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="31a85-1229">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="31a85-1230">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="31a85-1230">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="31a85-1231">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="31a85-1231">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="31a85-1232">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="31a85-1232">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="31a85-1233">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1233">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="31a85-1234">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1234">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="31a85-1235">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1235">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="31a85-1236">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1236">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="31a85-1237">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="31a85-1237">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="31a85-1238">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1238">Select **OK**.</span></span>

<span data-ttu-id="31a85-1239">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1239">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="31a85-1240">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1240">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="31a85-1241">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="31a85-1241">Configuration of IIS with web.config</span></span>

<span data-ttu-id="31a85-1242">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="31a85-1242">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="31a85-1243">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="31a85-1243">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="31a85-1244">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="31a85-1244">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="31a85-1245">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="31a85-1245">For more information, see the following topics:</span></span>

* [<span data-ttu-id="31a85-1246">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="31a85-1246">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="31a85-1247">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="31a85-1247">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="31a85-1248">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="31a85-1248">Configuration sections of web.config</span></span>

<span data-ttu-id="31a85-1249">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="31a85-1249">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="31a85-1250">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1250">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="31a85-1251">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1251">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="31a85-1252">应用程序池</span><span class="sxs-lookup"><span data-stu-id="31a85-1252">Application Pools</span></span>

<span data-ttu-id="31a85-1253">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="31a85-1253">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="31a85-1254">托管在进程内：应用需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-1254">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="31a85-1255">托管在进程外：建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="31a85-1255">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="31a85-1256">IIS“添加网站”对话框默认为每应用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1256">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="31a85-1257">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1257">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="31a85-1258">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1258">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="31a85-1259">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="31a85-1259">Application Pool Identity</span></span>

<span data-ttu-id="31a85-1260">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="31a85-1260">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="31a85-1261">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="31a85-1261">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="31a85-1262">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="31a85-1262">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="31a85-1264">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="31a85-1264">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="31a85-1265">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="31a85-1265">Resources can be secured using this identity.</span></span> <span data-ttu-id="31a85-1266">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="31a85-1266">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="31a85-1267">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="31a85-1267">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="31a85-1268">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="31a85-1268">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="31a85-1269">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1269">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="31a85-1270">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="31a85-1270">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="31a85-1271">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="31a85-1271">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="31a85-1272">在“输入要选择的对象名称”区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1272">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="31a85-1273">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="31a85-1273">Select the **Check Names** button.</span></span> <span data-ttu-id="31a85-1274">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-1274">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="31a85-1275">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="31a85-1275">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="31a85-1276">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-1276">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="31a85-1277">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="31a85-1277">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="31a85-1279">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1279">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="31a85-1281">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-1281">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="31a85-1282">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-1282">Provide additional permissions as needed.</span></span>

<span data-ttu-id="31a85-1283">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-1283">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="31a85-1284">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="31a85-1284">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="31a85-1285">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="31a85-1285">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="31a85-1286">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="31a85-1286">HTTP/2 support</span></span>

<span data-ttu-id="31a85-1287">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="31a85-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="31a85-1288">进程内</span><span class="sxs-lookup"><span data-stu-id="31a85-1288">In-process</span></span>
  * <span data-ttu-id="31a85-1289">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-1289">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="31a85-1290">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="31a85-1290">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="31a85-1291">进程外</span><span class="sxs-lookup"><span data-stu-id="31a85-1291">Out-of-process</span></span>
  * <span data-ttu-id="31a85-1292">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-1292">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="31a85-1293">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="31a85-1293">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="31a85-1294">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="31a85-1294">TLS 1.2 or later connection</span></span>

<span data-ttu-id="31a85-1295">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1295">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="31a85-1296">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1296">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="31a85-1297">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1297">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="31a85-1298">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="31a85-1298">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="31a85-1299">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="31a85-1299">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="31a85-1300">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1300">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="31a85-1301">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="31a85-1301">CORS preflight requests</span></span>

<span data-ttu-id="31a85-1302">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1302">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="31a85-1303">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1303">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="31a85-1304">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1304">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="31a85-1305">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="31a85-1305">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="31a85-1306">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="31a85-1306">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="31a85-1307">[应用程序初始化模块](#application-initialization-module)：可以将托管在[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)的应用配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="31a85-1307">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="31a85-1308">[空闲超时](#idle-timeout)：可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段不超时。</span><span class="sxs-lookup"><span data-stu-id="31a85-1308">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="31a85-1309">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="31a85-1309">Application Initialization Module</span></span>

<span data-ttu-id="31a85-1310">适用于托管在进程内和进程外的应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1310">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="31a85-1311">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-1311">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="31a85-1312">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="31a85-1312">The request triggers the app to start.</span></span> <span data-ttu-id="31a85-1313">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="31a85-1313">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="31a85-1314">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="31a85-1314">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="31a85-1315">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="31a85-1315">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="31a85-1316">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="31a85-1316">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="31a85-1317">打开“Internet Information Services”>“万维网服务”>“应用程序开发功能”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1317">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="31a85-1318">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="31a85-1318">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="31a85-1319">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="31a85-1319">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="31a85-1320">打开“添加角色和功能向导”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1320">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="31a85-1321">在“选择角色服务”面板中，打开“应用程序开发”节点 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1321">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="31a85-1322">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="31a85-1322">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="31a85-1323">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="31a85-1323">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="31a85-1324">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="31a85-1324">Using IIS Manager:</span></span>

  1. <span data-ttu-id="31a85-1325">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1325">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="31a85-1326">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="31a85-1327">默认的“启动模式”为“按需” 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1327">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="31a85-1328">将“启动模式”设置为“始终运行”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1328">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="31a85-1329">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1329">Select **OK**.</span></span>
  1. <span data-ttu-id="31a85-1330">打开“连接”面板中的“网站”节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-1330">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="31a85-1331">右键单击应用，并选择“管理网站”>“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1331">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="31a85-1332">默认的“预加载已启用”设置为“False” 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1332">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="31a85-1333">将“预加载已启用”设置为“True”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1333">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="31a85-1334">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1334">Select **OK**.</span></span>

* <span data-ttu-id="31a85-1335">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中 ：</span><span class="sxs-lookup"><span data-stu-id="31a85-1335">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="31a85-1336">空闲超时</span><span class="sxs-lookup"><span data-stu-id="31a85-1336">Idle Timeout</span></span>

<span data-ttu-id="31a85-1337">仅适用于托管在进程内的应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1337">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="31a85-1338">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="31a85-1338">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="31a85-1339">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1339">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="31a85-1340">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1340">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="31a85-1341">默认的“空闲超时(分钟)”为“20”分钟 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1341">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="31a85-1342">将“空闲超时(分钟)”设置为“0”（零） 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1342">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="31a85-1343">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1343">Select **OK**.</span></span>
1. <span data-ttu-id="31a85-1344">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="31a85-1344">Recycle the worker process.</span></span>

<span data-ttu-id="31a85-1345">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="31a85-1345">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="31a85-1346">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="31a85-1346">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="31a85-1347">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1347">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="31a85-1348">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="31a85-1348">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="31a85-1349">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="31a85-1349">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="31a85-1350">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1350">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="31a85-1351">[应用程序池 \<processModel> 的进程模型设置](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1351">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="31a85-1352">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="31a85-1352">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="31a85-1353">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="31a85-1353">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="31a85-1354">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="31a85-1354">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="31a85-1355">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="31a85-1355">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="31a85-1356">其他资源</span><span class="sxs-lookup"><span data-stu-id="31a85-1356">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="31a85-1357">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="31a85-1357">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="31a85-1358">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="31a85-1358">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="31a85-1359">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="31a85-1359">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="31a85-1360">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1360">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="31a85-1361">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="31a85-1361">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="31a85-1362">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="31a85-1362">Supported operating systems</span></span>

<span data-ttu-id="31a85-1363">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="31a85-1363">The following operating systems are supported:</span></span>

* <span data-ttu-id="31a85-1364">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-1364">Windows 7 or later</span></span>
* <span data-ttu-id="31a85-1365">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-1365">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="31a85-1366">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1366">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="31a85-1367">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1367">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="31a85-1368">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1368">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="31a85-1369">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1369">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="31a85-1370">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="31a85-1370">Supported platforms</span></span>

<span data-ttu-id="31a85-1371">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1371">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="31a85-1372">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="31a85-1372">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="31a85-1373">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="31a85-1373">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="31a85-1374">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="31a85-1374">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="31a85-1375">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="31a85-1375">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="31a85-1376">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1376">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="31a85-1377">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-1377">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="31a85-1378">ASP.NET Core 随附有 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，后者是默认的跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="31a85-1378">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="31a85-1379">使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用在独立于 IIS 工作进程（进程外）和 [Kestrel 服务器](xref:fundamentals/servers/index#kestrel)的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-1379">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="31a85-1380">由于 ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，因此该模块会处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="31a85-1380">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="31a85-1381">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1381">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="31a85-1382">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="31a85-1382">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="31a85-1383">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="31a85-1383">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="31a85-1385">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1385">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="31a85-1386">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1386">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="31a85-1387">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="31a85-1387">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="31a85-1388">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1388">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="31a85-1389">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-1389">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="31a85-1390">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="31a85-1390">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="31a85-1391">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1391">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="31a85-1392">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="31a85-1392">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="31a85-1393">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="31a85-1393">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="31a85-1394">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="31a85-1394">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="31a85-1395">`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的基础路径和端口来启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="31a85-1395">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="31a85-1396">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="31a85-1396">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="31a85-1397">`CreateDefaultBuilder` 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 方法。</span><span class="sxs-lookup"><span data-stu-id="31a85-1397">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="31a85-1398">`UseIISIntegration` 配置在 localhost IP 地址 (`127.0.0.1`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="31a85-1398">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="31a85-1399">如果动态端口为 1234，则 Kestrel 在 `127.0.0.1:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="31a85-1399">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="31a85-1400">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="31a85-1400">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="31a85-1401">Kestrel 的侦听 API</span><span class="sxs-lookup"><span data-stu-id="31a85-1401">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="31a85-1402">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="31a85-1402">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="31a85-1403">使用模块时，不需要调用 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="31a85-1403">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="31a85-1404">如果调用 `UseUrls` 或 `Listen`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="31a85-1404">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="31a85-1405">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1405">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="31a85-1406">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1406">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="31a85-1407">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="31a85-1407">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="31a85-1408">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="31a85-1408">Enable the IISIntegration components</span></span>

<span data-ttu-id="31a85-1409">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="31a85-1409">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="31a85-1410">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1410">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="31a85-1411">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="31a85-1411">IIS options</span></span>

| <span data-ttu-id="31a85-1412">选项</span><span class="sxs-lookup"><span data-stu-id="31a85-1412">Option</span></span>                         | <span data-ttu-id="31a85-1413">默认</span><span class="sxs-lookup"><span data-stu-id="31a85-1413">Default</span></span> | <span data-ttu-id="31a85-1414">设置</span><span class="sxs-lookup"><span data-stu-id="31a85-1414">Setting</span></span> |
| ---
<span data-ttu-id="31a85-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1416">'Blazor'</span></span>
- <span data-ttu-id="31a85-1417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1417">'Identity'</span></span>
- <span data-ttu-id="31a85-1418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1418">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1419">'Razor'</span></span>
- <span data-ttu-id="31a85-1420">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1422">'Blazor'</span></span>
- <span data-ttu-id="31a85-1423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1423">'Identity'</span></span>
- <span data-ttu-id="31a85-1424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1424">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1425">'Razor'</span></span>
- <span data-ttu-id="31a85-1426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1428">'Blazor'</span></span>
- <span data-ttu-id="31a85-1429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1429">'Identity'</span></span>
- <span data-ttu-id="31a85-1430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1430">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1431">'Razor'</span></span>
- <span data-ttu-id="31a85-1432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1434">'Blazor'</span></span>
- <span data-ttu-id="31a85-1435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1435">'Identity'</span></span>
- <span data-ttu-id="31a85-1436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1436">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1437">'Razor'</span></span>
- <span data-ttu-id="31a85-1438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1440">'Blazor'</span></span>
- <span data-ttu-id="31a85-1441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1441">'Identity'</span></span>
- <span data-ttu-id="31a85-1442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1442">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1443">'Razor'</span></span>
- <span data-ttu-id="31a85-1444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1446">'Blazor'</span></span>
- <span data-ttu-id="31a85-1447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1447">'Identity'</span></span>
- <span data-ttu-id="31a85-1448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1448">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1449">'Razor'</span></span>
- <span data-ttu-id="31a85-1450">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1452">'Blazor'</span></span>
- <span data-ttu-id="31a85-1453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1453">'Identity'</span></span>
- <span data-ttu-id="31a85-1454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1454">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1455">'Razor'</span></span>
- <span data-ttu-id="31a85-1456">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1458">'Blazor'</span></span>
- <span data-ttu-id="31a85-1459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1459">'Identity'</span></span>
- <span data-ttu-id="31a85-1460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1460">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1461">'Razor'</span></span>
- <span data-ttu-id="31a85-1462">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1464">'Blazor'</span></span>
- <span data-ttu-id="31a85-1465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1465">'Identity'</span></span>
- <span data-ttu-id="31a85-1466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1466">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1467">'Razor'</span></span>
- <span data-ttu-id="31a85-1468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1470">'Blazor'</span></span>
- <span data-ttu-id="31a85-1471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1471">'Identity'</span></span>
- <span data-ttu-id="31a85-1472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1472">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1473">'Razor'</span></span>
- <span data-ttu-id="31a85-1474">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1476">'Blazor'</span></span>
- <span data-ttu-id="31a85-1477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1477">'Identity'</span></span>
- <span data-ttu-id="31a85-1478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1478">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1479">'Razor'</span></span>
- <span data-ttu-id="31a85-1480">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1482">'Blazor'</span></span>
- <span data-ttu-id="31a85-1483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1483">'Identity'</span></span>
- <span data-ttu-id="31a85-1484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1484">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1485">'Razor'</span></span>
- <span data-ttu-id="31a85-1486">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1488">'Blazor'</span></span>
- <span data-ttu-id="31a85-1489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1489">'Identity'</span></span>
- <span data-ttu-id="31a85-1490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1490">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1491">'Razor'</span></span>
- <span data-ttu-id="31a85-1492">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1492">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1494">'Blazor'</span></span>
- <span data-ttu-id="31a85-1495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1495">'Identity'</span></span>
- <span data-ttu-id="31a85-1496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1496">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1497">'Razor'</span></span>
- <span data-ttu-id="31a85-1498">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1498">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-1499">---- | | `AutomaticAuthentication`      | `true`  | 如果为 `true`，IIS 服务器会将 `HttpContext.User` 设置为进行 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1499">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="31a85-1500">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="31a85-1500">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="31a85-1501">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-1501">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="31a85-1502">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1502">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="31a85-1503">| | `AuthenticationDisplayName`    | `null`  | 设置在登录页面上向用户显示的显示名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-1503">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="31a85-1504">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-1504">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="31a85-1505">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="31a85-1505">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="31a85-1506">选项</span><span class="sxs-lookup"><span data-stu-id="31a85-1506">Option</span></span>                         | <span data-ttu-id="31a85-1507">默认</span><span class="sxs-lookup"><span data-stu-id="31a85-1507">Default</span></span> | <span data-ttu-id="31a85-1508">设置</span><span class="sxs-lookup"><span data-stu-id="31a85-1508">Setting</span></span> |
| ---
<span data-ttu-id="31a85-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1510">'Blazor'</span></span>
- <span data-ttu-id="31a85-1511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1511">'Identity'</span></span>
- <span data-ttu-id="31a85-1512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1512">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1513">'Razor'</span></span>
- <span data-ttu-id="31a85-1514">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1516">'Blazor'</span></span>
- <span data-ttu-id="31a85-1517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1517">'Identity'</span></span>
- <span data-ttu-id="31a85-1518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1518">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1519">'Razor'</span></span>
- <span data-ttu-id="31a85-1520">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1520">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1522">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1522">'Blazor'</span></span>
- <span data-ttu-id="31a85-1523">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1523">'Identity'</span></span>
- <span data-ttu-id="31a85-1524">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1524">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1525">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1525">'Razor'</span></span>
- <span data-ttu-id="31a85-1526">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1526">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1528">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1528">'Blazor'</span></span>
- <span data-ttu-id="31a85-1529">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1529">'Identity'</span></span>
- <span data-ttu-id="31a85-1530">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1530">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1531">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1531">'Razor'</span></span>
- <span data-ttu-id="31a85-1532">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1532">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1534">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1534">'Blazor'</span></span>
- <span data-ttu-id="31a85-1535">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1535">'Identity'</span></span>
- <span data-ttu-id="31a85-1536">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1536">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1537">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1537">'Razor'</span></span>
- <span data-ttu-id="31a85-1538">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1538">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1540">'Blazor'</span></span>
- <span data-ttu-id="31a85-1541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1541">'Identity'</span></span>
- <span data-ttu-id="31a85-1542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1542">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1543">'Razor'</span></span>
- <span data-ttu-id="31a85-1544">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1546">'Blazor'</span></span>
- <span data-ttu-id="31a85-1547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1547">'Identity'</span></span>
- <span data-ttu-id="31a85-1548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1548">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1549">'Razor'</span></span>
- <span data-ttu-id="31a85-1550">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1552">'Blazor'</span></span>
- <span data-ttu-id="31a85-1553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1553">'Identity'</span></span>
- <span data-ttu-id="31a85-1554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1554">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1555">'Razor'</span></span>
- <span data-ttu-id="31a85-1556">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1558">'Blazor'</span></span>
- <span data-ttu-id="31a85-1559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1559">'Identity'</span></span>
- <span data-ttu-id="31a85-1560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1560">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1561">'Razor'</span></span>
- <span data-ttu-id="31a85-1562">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1564">'Blazor'</span></span>
- <span data-ttu-id="31a85-1565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1565">'Identity'</span></span>
- <span data-ttu-id="31a85-1566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1566">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1567">'Razor'</span></span>
- <span data-ttu-id="31a85-1568">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1570">'Blazor'</span></span>
- <span data-ttu-id="31a85-1571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1571">'Identity'</span></span>
- <span data-ttu-id="31a85-1572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1572">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1573">'Razor'</span></span>
- <span data-ttu-id="31a85-1574">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1576">'Blazor'</span></span>
- <span data-ttu-id="31a85-1577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1577">'Identity'</span></span>
- <span data-ttu-id="31a85-1578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1578">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1579">'Razor'</span></span>
- <span data-ttu-id="31a85-1580">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31a85-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1582">'Blazor'</span></span>
- <span data-ttu-id="31a85-1583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1583">'Identity'</span></span>
- <span data-ttu-id="31a85-1584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1584">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1585">'Razor'</span></span>
- <span data-ttu-id="31a85-1586">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1586">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31a85-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31a85-1588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1588">'Blazor'</span></span>
- <span data-ttu-id="31a85-1589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31a85-1589">'Identity'</span></span>
- <span data-ttu-id="31a85-1590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31a85-1590">'Let's Encrypt'</span></span>
- <span data-ttu-id="31a85-1591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31a85-1591">'Razor'</span></span>
- <span data-ttu-id="31a85-1592">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="31a85-1592">'SignalR' uid:</span></span> 

<span data-ttu-id="31a85-1593">---- | | `AutomaticAuthentication`      | `true`  | 如果为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)会将 `HttpContext.User` 设置为进行 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1593">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="31a85-1594">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="31a85-1594">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="31a85-1595">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-1595">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="31a85-1596">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="31a85-1596">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="31a85-1597">| | `AuthenticationDisplayName`    | `null`  | 设置在登录页面上向用户显示的显示名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-1597">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="31a85-1598">| | `ForwardClientCertificate`     | `true`  | 如果为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求标头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1598">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="31a85-1599">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="31a85-1599">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="31a85-1600">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="31a85-1600">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="31a85-1601">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-1601">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="31a85-1602">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1602">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="31a85-1603">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="31a85-1603">web.config file</span></span>

<span data-ttu-id="31a85-1604">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1604">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="31a85-1605">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1605">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="31a85-1606">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1606">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="31a85-1607">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="31a85-1607">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="31a85-1608">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)  。</span><span class="sxs-lookup"><span data-stu-id="31a85-1608">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="31a85-1609">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="31a85-1609">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="31a85-1610">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="31a85-1610">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="31a85-1611">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="31a85-1611">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="31a85-1612">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="31a85-1612">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="31a85-1613">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="31a85-1613">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="31a85-1614">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="31a85-1614">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="31a85-1615">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1615">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="31a85-1616">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="31a85-1616">web.config file location</span></span>

<span data-ttu-id="31a85-1617">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config 文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1617">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="31a85-1618">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="31a85-1618">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="31a85-1619">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1619">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="31a85-1620">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="31a85-1620">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="31a85-1621">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="31a85-1621">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="31a85-1622">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1622">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="31a85-1623">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="31a85-1623">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="31a85-1624">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="31a85-1624">Transform web.config</span></span>

<span data-ttu-id="31a85-1625">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1625">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="31a85-1626">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="31a85-1626">IIS configuration</span></span>

<span data-ttu-id="31a85-1627">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="31a85-1627">**Windows Server operating systems**</span></span>

<span data-ttu-id="31a85-1628">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="31a85-1628">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="31a85-1629">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="31a85-1629">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="31a85-1630">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1630">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="31a85-1632">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="31a85-1632">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="31a85-1633">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="31a85-1633">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="31a85-1635">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-1635">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="31a85-1636">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1636">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="31a85-1637">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-1637">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="31a85-1638">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1638">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="31a85-1639">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-1639">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="31a85-1640">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-1640">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="31a85-1641">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1641">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="31a85-1642">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-1642">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="31a85-1643">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1643">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="31a85-1644">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="31a85-1644">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="31a85-1645">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="31a85-1645">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="31a85-1646">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="31a85-1646">**Windows desktop operating systems**</span></span>

<span data-ttu-id="31a85-1647">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1647">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="31a85-1648">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="31a85-1648">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="31a85-1649">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-1649">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="31a85-1650">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-1650">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="31a85-1651">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="31a85-1651">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="31a85-1652">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="31a85-1652">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="31a85-1653">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-1653">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="31a85-1654">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-1654">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="31a85-1655">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1655">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="31a85-1656">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-1656">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="31a85-1657">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1657">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="31a85-1658">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-1658">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="31a85-1659">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-1659">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="31a85-1660">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1660">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="31a85-1661">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="31a85-1661">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="31a85-1662">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1662">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="31a85-1663">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="31a85-1663">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="31a85-1665">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="31a85-1665">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="31a85-1666">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="31a85-1666">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="31a85-1667">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1667">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="31a85-1668">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="31a85-1668">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="31a85-1669">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="31a85-1669">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="31a85-1670">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1670">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="31a85-1671">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="31a85-1671">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="31a85-1672">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1672">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="31a85-1673">下载</span><span class="sxs-lookup"><span data-stu-id="31a85-1673">Download</span></span>

1. <span data-ttu-id="31a85-1674">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="31a85-1674">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="31a85-1675">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-1675">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="31a85-1676">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="31a85-1676">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="31a85-1677">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1677">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="31a85-1678">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="31a85-1678">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="31a85-1679">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1679">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="31a85-1680">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="31a85-1680">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="31a85-1681">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1681">Run the installer on the server.</span></span> <span data-ttu-id="31a85-1682">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="31a85-1682">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="31a85-1683">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="31a85-1683">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="31a85-1684">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-1684">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="31a85-1685">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1685">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="31a85-1686">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="31a85-1686">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="31a85-1687">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1687">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="31a85-1688">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-1688">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="31a85-1689">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="31a85-1689">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="31a85-1690">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-1690">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="31a85-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (applicationHost.config) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="31a85-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="31a85-1692">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1692">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="31a85-1693">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1693">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="31a85-1694">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="31a85-1694">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="31a85-1695">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="31a85-1695">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="31a85-1696">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="31a85-1696">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="31a85-1697">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="31a85-1697">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="31a85-1698">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="31a85-1698">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="31a85-1699">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="31a85-1699">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="31a85-1700">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="31a85-1700">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="31a85-1701">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="31a85-1701">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="31a85-1702">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1702">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="31a85-1703">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="31a85-1703">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="31a85-1704">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="31a85-1704">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="31a85-1705">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1705">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="31a85-1706">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="31a85-1706">The preferred method is to use WebPI.</span></span> <span data-ttu-id="31a85-1707">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-1707">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="31a85-1708">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="31a85-1708">Create the IIS site</span></span>

1. <span data-ttu-id="31a85-1709">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1709">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="31a85-1710">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="31a85-1710">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="31a85-1711">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1711">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="31a85-1712">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="31a85-1712">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="31a85-1713">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="31a85-1713">Right-click the **Sites** folder.</span></span> <span data-ttu-id="31a85-1714">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1714">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="31a85-1715">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1715">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="31a85-1716">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="31a85-1716">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="31a85-1718">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="31a85-1718">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="31a85-1719">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="31a85-1719">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="31a85-1720">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="31a85-1720">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="31a85-1721">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="31a85-1721">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="31a85-1722">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="31a85-1722">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="31a85-1723">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1723">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="31a85-1724">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1724">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="31a85-1725">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1725">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="31a85-1726">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="31a85-1726">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="31a85-1728">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="31a85-1728">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="31a85-1729">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1729">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="31a85-1730">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="31a85-1730">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="31a85-1731">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1731">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="31a85-1732">在 IIS 管理器 >“应用程序池”的“操作”侧栏中，选择“设置应用程序池默认设置”或“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1732">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="31a85-1733">找到“启用 32 位应用程序”并将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1733">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="31a85-1734">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1734">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="31a85-1735">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-1735">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="31a85-1736">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="31a85-1736">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="31a85-1737">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1737">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="31a85-1738">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="31a85-1738">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="31a85-1739">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1739">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="31a85-1740">部署应用</span><span class="sxs-lookup"><span data-stu-id="31a85-1740">Deploy the app</span></span>

<span data-ttu-id="31a85-1741">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="31a85-1741">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="31a85-1742">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="31a85-1742">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="31a85-1743">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="31a85-1743">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="31a85-1744">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1744">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="31a85-1745">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="31a85-1745">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="31a85-1747">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="31a85-1747">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="31a85-1748">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1748">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="31a85-1749">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="31a85-1749">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="31a85-1750">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="31a85-1750">Alternatives to Web Deploy</span></span>

<span data-ttu-id="31a85-1751">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="31a85-1751">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="31a85-1752">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="31a85-1752">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="31a85-1753">浏览网站</span><span class="sxs-lookup"><span data-stu-id="31a85-1753">Browse the website</span></span>

<span data-ttu-id="31a85-1754">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-1754">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="31a85-1755">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1755">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="31a85-1756">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="31a85-1756">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="31a85-1758">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="31a85-1758">Locked deployment files</span></span>

<span data-ttu-id="31a85-1759">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="31a85-1759">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="31a85-1760">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1760">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="31a85-1761">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="31a85-1761">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="31a85-1762">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1762">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="31a85-1763">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1763">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="31a85-1764">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1764">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="31a85-1765">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1765">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="31a85-1766">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1766">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="31a85-1767">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="31a85-1767">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="31a85-1768">数据保护</span><span class="sxs-lookup"><span data-stu-id="31a85-1768">Data protection</span></span>

<span data-ttu-id="31a85-1769">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1769">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="31a85-1770">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1770">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="31a85-1771">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="31a85-1771">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="31a85-1772">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="31a85-1772">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="31a85-1773">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="31a85-1773">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="31a85-1774">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="31a85-1774">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="31a85-1775">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="31a85-1775">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="31a85-1776">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1776">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="31a85-1777">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="31a85-1777">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="31a85-1778">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="31a85-1778">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="31a85-1779">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1779">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="31a85-1780">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="31a85-1780">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="31a85-1781">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1781">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="31a85-1782">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="31a85-1782">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="31a85-1783">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="31a85-1783">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="31a85-1784">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="31a85-1784">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="31a85-1785">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="31a85-1785">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="31a85-1786">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="31a85-1786">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="31a85-1787">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="31a85-1787">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="31a85-1788">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1788">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="31a85-1789">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1789">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="31a85-1790">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="31a85-1790">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="31a85-1791">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1791">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="31a85-1792">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1792">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="31a85-1793">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="31a85-1793">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="31a85-1794">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1794">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="31a85-1795">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1795">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="31a85-1796">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1796">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="31a85-1797">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1797">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="31a85-1798">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="31a85-1798">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="31a85-1799">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="31a85-1799">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="31a85-1800">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1800">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="31a85-1801">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="31a85-1801">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="31a85-1802">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1802">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="31a85-1803">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="31a85-1803">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="31a85-1804">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1804">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="31a85-1805">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="31a85-1805">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="31a85-1806">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1806">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="31a85-1807">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="31a85-1807">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="31a85-1808">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="31a85-1808">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="31a85-1809">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="31a85-1809">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="31a85-1810">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1810">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="31a85-1811">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="31a85-1811">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="31a85-1812">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1812">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="31a85-1813">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1813">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="31a85-1814">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="31a85-1814">Virtual Directories</span></span>

<span data-ttu-id="31a85-1815">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1815">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="31a85-1816">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1816">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="31a85-1817">子应用程序</span><span class="sxs-lookup"><span data-stu-id="31a85-1817">Sub-applications</span></span>

<span data-ttu-id="31a85-1818">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1818">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="31a85-1819">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="31a85-1819">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="31a85-1820">子应用不应将 ASP.NET Core 模块作为处理程序包含在其中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1820">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="31a85-1821">如果在子应用的 web.config 文件中将该模块添加为处理程序，则在尝试浏览子应用时会收到“500.19 内部服务器错误”，即引用错误的配置文件。</span><span class="sxs-lookup"><span data-stu-id="31a85-1821">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="31a85-1822">以下示例显示 ASP.NET Core 子应用的已发布 web.config 文件：</span><span class="sxs-lookup"><span data-stu-id="31a85-1822">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="31a85-1823">在 ASP.NET Core 应用下托管非 ASP.NET Core 子应用时，需在子应用的 web.config 文件中显式删除继承的处理程序：</span><span class="sxs-lookup"><span data-stu-id="31a85-1823">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

<span data-ttu-id="31a85-1824">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="31a85-1824">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="31a85-1825">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="31a85-1825">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="31a85-1826">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1826">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="31a85-1827">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="31a85-1827">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="31a85-1828">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="31a85-1828">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="31a85-1829">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="31a85-1829">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="31a85-1830">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="31a85-1830">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="31a85-1831">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="31a85-1831">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="31a85-1832">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1832">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="31a85-1833">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1833">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="31a85-1834">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="31a85-1834">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="31a85-1835">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1835">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="31a85-1836">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="31a85-1836">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="31a85-1837">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1837">Select **OK**.</span></span>

<span data-ttu-id="31a85-1838">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1838">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="31a85-1839">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="31a85-1839">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="31a85-1840">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="31a85-1840">Configuration of IIS with web.config</span></span>

<span data-ttu-id="31a85-1841">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="31a85-1841">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="31a85-1842">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="31a85-1842">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="31a85-1843">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="31a85-1843">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="31a85-1844">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="31a85-1844">For more information, see the following topics:</span></span>

* [<span data-ttu-id="31a85-1845">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="31a85-1845">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="31a85-1846">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="31a85-1846">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="31a85-1847">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="31a85-1847">Configuration sections of web.config</span></span>

<span data-ttu-id="31a85-1848">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="31a85-1848">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="31a85-1849">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="31a85-1849">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="31a85-1850">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1850">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="31a85-1851">应用程序池</span><span class="sxs-lookup"><span data-stu-id="31a85-1851">Application Pools</span></span>

<span data-ttu-id="31a85-1852">在服务器上托管多个网站时，建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="31a85-1852">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="31a85-1853">IIS“添加网站”对话框默认执行此配置。</span><span class="sxs-lookup"><span data-stu-id="31a85-1853">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="31a85-1854">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1854">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="31a85-1855">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="31a85-1855">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="31a85-1856">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="31a85-1856">Application Pool Identity</span></span>

<span data-ttu-id="31a85-1857">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="31a85-1857">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="31a85-1858">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="31a85-1858">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="31a85-1859">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="31a85-1859">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="31a85-1861">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="31a85-1861">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="31a85-1862">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="31a85-1862">Resources can be secured using this identity.</span></span> <span data-ttu-id="31a85-1863">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="31a85-1863">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="31a85-1864">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="31a85-1864">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="31a85-1865">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="31a85-1865">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="31a85-1866">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1866">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="31a85-1867">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="31a85-1867">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="31a85-1868">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="31a85-1868">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="31a85-1869">在“输入要选择的对象名称”区域中输入**IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="31a85-1869">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="31a85-1870">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="31a85-1870">Select the **Check Names** button.</span></span> <span data-ttu-id="31a85-1871">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-1871">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="31a85-1872">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="31a85-1872">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="31a85-1873">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="31a85-1873">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="31a85-1874">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="31a85-1874">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="31a85-1876">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="31a85-1876">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="31a85-1878">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-1878">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="31a85-1879">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-1879">Provide additional permissions as needed.</span></span>

<span data-ttu-id="31a85-1880">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="31a85-1880">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="31a85-1881">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="31a85-1881">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="31a85-1882">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="31a85-1882">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="31a85-1883">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="31a85-1883">HTTP/2 support</span></span>

<span data-ttu-id="31a85-1884">满足以下基本要求的进程外部署支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="31a85-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="31a85-1885">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="31a85-1885">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="31a85-1886">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="31a85-1886">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="31a85-1887">目标框架：不适用于进程外部署，因为 HTTP/2 连接完全由 IIS 处理。</span><span class="sxs-lookup"><span data-stu-id="31a85-1887">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="31a85-1888">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="31a85-1888">TLS 1.2 or later connection</span></span>

<span data-ttu-id="31a85-1889">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="31a85-1889">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="31a85-1890">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="31a85-1890">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="31a85-1891">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="31a85-1891">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="31a85-1892">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1892">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="31a85-1893">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="31a85-1893">CORS preflight requests</span></span>

<span data-ttu-id="31a85-1894">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1894">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="31a85-1895">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="31a85-1895">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="31a85-1896">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="31a85-1896">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="31a85-1897">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="31a85-1897">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="31a85-1898">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="31a85-1898">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="31a85-1899">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="31a85-1899">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="31a85-1900">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="31a85-1900">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="31a85-1901">其他资源</span><span class="sxs-lookup"><span data-stu-id="31a85-1901">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="31a85-1902">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="31a85-1902">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="31a85-1903">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="31a85-1903">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="31a85-1904">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="31a85-1904">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
