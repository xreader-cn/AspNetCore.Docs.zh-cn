---
title: 使用 IIS 和 ASP.NET Core 进行进程外托管
author: rick-anderson
description: 了解如何使用 IIS 和 ASP.NET Core 模块进行进程外托管。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060412"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="d6448-103">使用 IIS 和 ASP.NET Core 进行进程外托管</span><span class="sxs-lookup"><span data-stu-id="d6448-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="d6448-104">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="d6448-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="d6448-105">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="d6448-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="d6448-106">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="d6448-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d6448-107">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="d6448-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="d6448-109">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="d6448-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="d6448-110">驱动程序将请求路由到网站的配置端口上的 IIS。</span><span class="sxs-lookup"><span data-stu-id="d6448-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="d6448-111">配置的端口通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="d6448-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="d6448-112">此模块将该请求转发到应用的随机端口上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d6448-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="d6448-113">随机端口不是 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="d6448-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="d6448-114">ASP.NET Core 模块在启动时通过环境变量指定端口。</span><span class="sxs-lookup"><span data-stu-id="d6448-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="d6448-115"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="d6448-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="d6448-116">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="d6448-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="d6448-117">此模块不支持 HTTPS 转发。</span><span class="sxs-lookup"><span data-stu-id="d6448-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="d6448-118">即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="d6448-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="d6448-119">Kestrel 从模块获取请求后，请求会被转发到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="d6448-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d6448-120">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="d6448-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d6448-121">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="d6448-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="d6448-122">应用的响应传递回 IIS，IIS 将响应转发回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="d6448-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="d6448-123">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="d6448-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d6448-124">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="d6448-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="d6448-125">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="d6448-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="d6448-126">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="d6448-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="d6448-127">在 `CreateHostBuilder` 中生成主机 (`Program.cs`)，请调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="d6448-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="d6448-128">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/generic-host#default-builder-settings>。</span><span class="sxs-lookup"><span data-stu-id="d6448-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="d6448-129">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="d6448-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="d6448-130">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="d6448-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="d6448-131">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="d6448-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="d6448-132">选项</span><span class="sxs-lookup"><span data-stu-id="d6448-132">Option</span></span>                         | <span data-ttu-id="d6448-133">默认</span><span class="sxs-lookup"><span data-stu-id="d6448-133">Default</span></span> | <span data-ttu-id="d6448-134">设置</span><span class="sxs-lookup"><span data-stu-id="d6448-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="d6448-135">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="d6448-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="d6448-136">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="d6448-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="d6448-137">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="d6448-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="d6448-138">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="d6448-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="d6448-139">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="d6448-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="d6448-140">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="d6448-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="d6448-141">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="d6448-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="d6448-142">将 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块配置为转发：</span><span class="sxs-lookup"><span data-stu-id="d6448-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="d6448-143">方案 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="d6448-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="d6448-144">发起请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="d6448-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="d6448-145">[IIS 集成中间件](#enable-the-iisintegration-components)配置转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="d6448-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="d6448-146">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="d6448-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="d6448-147">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="d6448-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="d6448-148">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="d6448-148">Out-of-process hosting model</span></span>

<span data-ttu-id="d6448-149">若要配置进程外托管应用，请在项目文件 ( `.csproj`) 中将 `<AspNetCoreHostingModel>` 属性的值设置为 `OutOfProcess`：</span><span class="sxs-lookup"><span data-stu-id="d6448-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="d6448-150">进程内托管设为 `InProcess`，这是默认值。</span><span class="sxs-lookup"><span data-stu-id="d6448-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="d6448-151">`<AspNetCoreHostingModel>` 的值不区分大小写，因此 `inprocess` 和 `outofprocess` 均为有效值。</span><span class="sxs-lookup"><span data-stu-id="d6448-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="d6448-152">使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，而不是 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="d6448-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="d6448-153">对于进程外托管，[`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) 会调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 来进行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d6448-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="d6448-154">在 ASP.NET Core 模块后运行时，配置服务器应侦听的端口和基本路径。</span><span class="sxs-lookup"><span data-stu-id="d6448-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="d6448-155">配置主机以捕获启动错误。</span><span class="sxs-lookup"><span data-stu-id="d6448-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="d6448-156">进程名</span><span class="sxs-lookup"><span data-stu-id="d6448-156">Process name</span></span>

<span data-ttu-id="d6448-157">`Process.GetCurrentProcess().ProcessName` 报告 `w3wp`/`iisexpress`（进程内）或 `dotnet`（进程外）。</span><span class="sxs-lookup"><span data-stu-id="d6448-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="d6448-158">许多本机模块（如 Windows 身份验证）仍处于活动状态。</span><span class="sxs-lookup"><span data-stu-id="d6448-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="d6448-159">要详细了解随 ASP.NET Core 模块活动的 IIS 模块，请参阅 <xref:host-and-deploy/iis/modules>。</span><span class="sxs-lookup"><span data-stu-id="d6448-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="d6448-160">ASP.NET Core 模块还可以：</span><span class="sxs-lookup"><span data-stu-id="d6448-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="d6448-161">为工作进程设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="d6448-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="d6448-162">将 stdout 输出记录到文件存储器，以解决启动问题。</span><span class="sxs-lookup"><span data-stu-id="d6448-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="d6448-163">转发 Windows 身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="d6448-163">Forward Windows authentication tokens.</span></span>
