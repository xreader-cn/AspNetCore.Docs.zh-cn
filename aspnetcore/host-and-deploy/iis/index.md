---
title: 使用 IIS 在 Windows 上托管 ASP.NET Core
author: guardrex
description: 了解如何在 Windows Server Internet Information Services (IIS) 上托管 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: host-and-deploy/iis/index
ms.openlocfilehash: 9392da14e589736b24790676c1c07c9964882737
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428455"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="6ccdb-103">使用 IIS 在 Windows 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ccdb-103">Host ASP.NET Core on Windows with IIS</span></span>

<span data-ttu-id="6ccdb-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6ccdb-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[<span data-ttu-id="6ccdb-105">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="6ccdb-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="6ccdb-106">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="6ccdb-106">Supported operating systems</span></span>

<span data-ttu-id="6ccdb-107">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="6ccdb-108">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6ccdb-108">Windows 7 or later</span></span>
* <span data-ttu-id="6ccdb-109">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6ccdb-109">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="6ccdb-110">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="6ccdb-111">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="6ccdb-112">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="6ccdb-113">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="6ccdb-113">Supported platforms</span></span>

<span data-ttu-id="6ccdb-114">支持针对 32 位 (x86) 和 64 位 (x64) 部署发布应用。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-114">Apps published for 32-bit (x86) and 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="6ccdb-115">除非应用符合以下条件，否则部署 32 位应用：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-115">Deploy a 32-bit app unless the app:</span></span>

* <span data-ttu-id="6ccdb-116">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-116">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="6ccdb-117">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-117">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="6ccdb-118">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-118">Has 64-bit native dependencies.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="6ccdb-119">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="6ccdb-119">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="6ccdb-120">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="6ccdb-120">Enable the IISIntegration components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="6ccdb-121">典型的 Program.cs 调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 以开始设置主机：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-121">A typical *Program.cs* calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to begin setting up a host:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="6ccdb-122">典型的 Program.cs 调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 以开始设置主机：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-122">A typical *Program.cs* calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to begin setting up a host:</span></span>

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6ccdb-123">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-123">**In-process hosting model**</span></span>

<span data-ttu-id="6ccdb-124">`CreateDefaultBuilder` 调用 `UseIIS` 方法以启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-124">`CreateDefaultBuilder` calls the `UseIIS` method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="6ccdb-125">性能测试表明，与在进程外托管应用并将请求代理到 [Kestrel](xref:fundamentals/servers/kestrel) 服务器相比，在进程中托管 .NET Core 应用可以大大提升请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-125">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="6ccdb-126">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-126">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="6ccdb-127">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-127">**Out-of-process hosting model**</span></span>

<span data-ttu-id="6ccdb-128">对于使用 IIS 的进程外托管，`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的基础路径和端口来启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-128">For out-of-process hosting with IIS, `CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="6ccdb-129">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-129">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="6ccdb-130">`CreateDefaultBuilder` 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 方法。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-130">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="6ccdb-131">`UseIISIntegration` 配置在 localhost IP 地址 (`127.0.0.1`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-131">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="6ccdb-132">如果动态端口为 1234，则 Kestrel 在 `127.0.0.1:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-132">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="6ccdb-133">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-133">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="6ccdb-134">Kestrel 的侦听 API</span><span class="sxs-lookup"><span data-stu-id="6ccdb-134">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="6ccdb-135">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="6ccdb-135">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="6ccdb-136">使用模块时，不需要调用 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-136">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="6ccdb-137">如果调用 `UseUrls` 或 `Listen`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-137">If `UseUrls` or `Listen` is called, Kestrel listens on the ports specified only when running the app without IIS.</span></span>

<span data-ttu-id="6ccdb-138">有关进程内和进程外托管模型的详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)和 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-138">For more information on the in-process and out-of-process hosting models, see [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="6ccdb-139">`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的基础路径和端口来启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-139">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="6ccdb-140">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-140">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="6ccdb-141">`CreateDefaultBuilder` 调用 [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) 方法。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-141">`CreateDefaultBuilder` calls the [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) method.</span></span> <span data-ttu-id="6ccdb-142">`UseIISIntegration` 配置在 localhost IP 地址 (`127.0.0.1`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-142">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="6ccdb-143">如果动态端口为 1234，则 Kestrel 在 `127.0.0.1:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-143">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="6ccdb-144">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-144">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="6ccdb-145">Kestrel 的侦听 API</span><span class="sxs-lookup"><span data-stu-id="6ccdb-145">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="6ccdb-146">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="6ccdb-146">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="6ccdb-147">使用模块时，不需要调用 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-147">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="6ccdb-148">如果调用 `UseUrls` 或 `Listen`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-148">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="6ccdb-149">`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的基础路径和端口来启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-149">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="6ccdb-150">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-150">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="6ccdb-151">`CreateDefaultBuilder` 调用 [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) 方法。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-151">`CreateDefaultBuilder` calls the [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) method.</span></span> <span data-ttu-id="6ccdb-152">`UseIISIntegration` 配置在 localhost IP 地址 (`localhost`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-152">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`localhost`).</span></span> <span data-ttu-id="6ccdb-153">如果动态端口为 1234，则 Kestrel 在 `localhost:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-153">If the dynamic port is 1234, Kestrel listens at `localhost:1234`.</span></span> <span data-ttu-id="6ccdb-154">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-154">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="6ccdb-155">Kestrel 的侦听 API</span><span class="sxs-lookup"><span data-stu-id="6ccdb-155">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="6ccdb-156">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="6ccdb-156">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="6ccdb-157">使用模块时，不需要调用 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-157">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="6ccdb-158">如果调用 `UseUrls` 或 `Listen`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-158">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="6ccdb-159">在应用依赖项中加入对 [Microsoft.AspNetCore.Server.IISIntegration ](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)包的依赖项。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-159">Include a dependency on the [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) package in the app's dependencies.</span></span> <span data-ttu-id="6ccdb-160">通过向 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 添加 [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) 扩展方法来使用 IIS 集成中间件：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-160">Use IIS Integration middleware by adding the [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) extension method to [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder):</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

<span data-ttu-id="6ccdb-161">同时需要 [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) 和 [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-161">Both [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) and [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions.useiisintegration) are required.</span></span> <span data-ttu-id="6ccdb-162">调用 `UseIISIntegration` 的代码不会影响代码可移植性。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-162">Code calling `UseIISIntegration` doesn't affect code portability.</span></span> <span data-ttu-id="6ccdb-163">如果应用不在 IIS 后面运行（例如，应用直接在 Kestrel 上运行），则 `UseIISIntegration` 不会运行。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-163">If the app isn't run behind IIS (for example, the app is run directly on Kestrel), `UseIISIntegration` doesn't operate.</span></span>

<span data-ttu-id="6ccdb-164">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-164">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="6ccdb-165">`UseIISIntegration` 配置在 localhost IP 地址 (`localhost`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-165">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`localhost`).</span></span> <span data-ttu-id="6ccdb-166">如果动态端口为 1234，则 Kestrel 在 `localhost:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-166">If the dynamic port is 1234, Kestrel listens at `localhost:1234`.</span></span> <span data-ttu-id="6ccdb-167">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-167">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* <span data-ttu-id="6ccdb-168">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="6ccdb-168">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="6ccdb-169">使用模块时无需调用 `UseUrls`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-169">A call to `UseUrls` isn't required when using the module.</span></span> <span data-ttu-id="6ccdb-170">如果调用 `UseUrls`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-170">If `UseUrls` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="6ccdb-171">如果在 ASP.NET Core 1.0 应用中调用 `UseUrls`，请在调用 `UseIISIntegration` 前调用它，使模块配置的端口不会被覆盖。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-171">If `UseUrls` is called in an ASP.NET Core 1.0 app, call it **before** calling `UseIISIntegration` so that the module-configured port isn't overwritten.</span></span> <span data-ttu-id="6ccdb-172">ASP.NET Core 1.1 不需要此调用顺序，因为模块设置会重写 `UseUrls`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-172">This calling order isn't required with ASP.NET Core 1.1 because the module setting overrides `UseUrls`.</span></span>

::: moniker-end

<span data-ttu-id="6ccdb-173">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/host/index)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-173">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/host/index).</span></span>

### <a name="iis-options"></a><span data-ttu-id="6ccdb-174">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="6ccdb-174">IIS options</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6ccdb-175">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-175">**In-process hosting model**</span></span>

<span data-ttu-id="6ccdb-176">若要配置 IIS 服务器选项，请在 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) 中加入适用于 [IISServerOptions](/dotnet/api/microsoft.aspnetcore.builder.iisserveroptions) 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-176">To configure IIS Server options, include a service configuration for [IISServerOptions](/dotnet/api/microsoft.aspnetcore.builder.iisserveroptions) in [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices).</span></span> <span data-ttu-id="6ccdb-177">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-177">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="6ccdb-178">选项</span><span class="sxs-lookup"><span data-stu-id="6ccdb-178">Option</span></span>                         | <span data-ttu-id="6ccdb-179">默认</span><span class="sxs-lookup"><span data-stu-id="6ccdb-179">Default</span></span> | <span data-ttu-id="6ccdb-180">设置</span><span class="sxs-lookup"><span data-stu-id="6ccdb-180">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="6ccdb-181">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-181">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="6ccdb-182">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-182">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="6ccdb-183">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-183">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="6ccdb-184">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-184">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="6ccdb-185">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-185">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="6ccdb-186">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-186">**Out-of-process hosting model**</span></span>

::: moniker-end

<span data-ttu-id="6ccdb-187">若要配置 IIS 选项，请在 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) 中加入适用于 [IISOptions](/dotnet/api/microsoft.aspnetcore.builder.iisoptions) 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-187">To configure IIS options, include a service configuration for [IISOptions](/dotnet/api/microsoft.aspnetcore.builder.iisoptions) in [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices).</span></span> <span data-ttu-id="6ccdb-188">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-188">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="6ccdb-189">选项</span><span class="sxs-lookup"><span data-stu-id="6ccdb-189">Option</span></span>                         | <span data-ttu-id="6ccdb-190">默认</span><span class="sxs-lookup"><span data-stu-id="6ccdb-190">Default</span></span> | <span data-ttu-id="6ccdb-191">设置</span><span class="sxs-lookup"><span data-stu-id="6ccdb-191">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="6ccdb-192">若为 `true`，IIS 集成中间件将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-192">If `true`, IIS Integration Middleware sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="6ccdb-193">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-193">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="6ccdb-194">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-194">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="6ccdb-195">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-195">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="6ccdb-196">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-196">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="6ccdb-197">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-197">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6ccdb-198">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="6ccdb-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6ccdb-199">配置转发头中间件的 IIS 集成中间件和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-199">The IIS Integration Middleware, which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="6ccdb-200">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-200">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="6ccdb-201">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-201">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="6ccdb-202">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="6ccdb-202">web.config file</span></span>

<span data-ttu-id="6ccdb-203">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-203">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6ccdb-204">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-204">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="6ccdb-205">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-205">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="6ccdb-206">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-206">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="6ccdb-207">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-207">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="6ccdb-208">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-208">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="6ccdb-209">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-209">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="6ccdb-210">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-210">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="6ccdb-211">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-211">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="6ccdb-212">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-212">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="6ccdb-213">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-213">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="6ccdb-214">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-214">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module).</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="6ccdb-215">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="6ccdb-215">web.config file location</span></span>

<span data-ttu-id="6ccdb-216">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)web.config 文件必须存在于已部署应用的内容根路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-216">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the content root path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="6ccdb-217">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-217">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="6ccdb-218">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-218">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="6ccdb-219">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-219">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="6ccdb-220">当存在 web.config 文件且站点正常启动的情况下，若要请求获取这些敏感文件，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-220">When the *web.config* file is present and and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="6ccdb-221">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-221">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="6ccdb-222">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-222">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="6ccdb-223">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="6ccdb-223">IIS configuration</span></span>

<span data-ttu-id="6ccdb-224">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-224">**Windows Server operating systems**</span></span>

<span data-ttu-id="6ccdb-225">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-225">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="6ccdb-226">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-226">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="6ccdb-227">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-227">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="6ccdb-229">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-229">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="6ccdb-230">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-230">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="6ccdb-232">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-232">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="6ccdb-233">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-233">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="6ccdb-234">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-234">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="6ccdb-235">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-235">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="6ccdb-236">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-236">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="6ccdb-237">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-237">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="6ccdb-238">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-238">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="6ccdb-239">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-239">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6ccdb-240">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-240">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="6ccdb-241">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-241">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="6ccdb-242">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-242">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="6ccdb-243">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-243">**Windows desktop operating systems**</span></span>

<span data-ttu-id="6ccdb-244">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-244">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="6ccdb-245">导航到“控制面板” > “程序” > “程序和功能” > “打开或关闭 Windows 功能”（位于屏幕左侧）。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-245">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="6ccdb-246">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-246">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="6ccdb-247">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-247">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="6ccdb-248">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-248">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="6ccdb-249">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-249">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="6ccdb-250">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-250">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="6ccdb-251">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-251">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="6ccdb-252">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-252">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="6ccdb-253">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-253">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="6ccdb-254">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-254">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="6ccdb-255">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-255">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="6ccdb-256">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-256">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="6ccdb-257">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-257">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="6ccdb-258">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-258">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6ccdb-259">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-259">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="6ccdb-260">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-260">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="6ccdb-262">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="6ccdb-262">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="6ccdb-263">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-263">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="6ccdb-264">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-264">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6ccdb-265">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-265">The module allows ASP.NET Core apps to run behind IIS.</span></span> <span data-ttu-id="6ccdb-266">如果系统没有 Internet 连接，请先获取并安装 [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840)，然后再安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-266">If the system doesn't have an Internet connection, obtain and install the [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840) before installing the .NET Core Hosting Bundle.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6ccdb-267">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-267">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="6ccdb-268">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-268">Run the Hosting Bundle installer again after installing IIS.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="6ccdb-269">直接下载（当前版本）</span><span class="sxs-lookup"><span data-stu-id="6ccdb-269">Direct download (current version)</span></span>

<span data-ttu-id="6ccdb-270">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-270">Download the installer using the following link:</span></span>

[<span data-ttu-id="6ccdb-271">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="6ccdb-271">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="6ccdb-272">先前版本的安装程序</span><span class="sxs-lookup"><span data-stu-id="6ccdb-272">Earlier versions of the installer</span></span>

<span data-ttu-id="6ccdb-273">若要获取先前版本的安装程序：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-273">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="6ccdb-274">导航到 [.NET 下载存档](https://www.microsoft.com/net/download/archives)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-274">Navigate to the [.NET download archives](https://www.microsoft.com/net/download/archives).</span></span>
1. <span data-ttu-id="6ccdb-275">在“.NET Core”下，选择 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-275">Under **.NET Core**, select the .NET Core version.</span></span>
1. <span data-ttu-id="6ccdb-276">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-276">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="6ccdb-277">使用“运行时和托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-277">Download the installer using the **Runtime & Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="6ccdb-278">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-278">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="6ccdb-279">有关详细信息，请参阅[支持策略](https://www.microsoft.com/net/download/dotnet-core/2.0)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-279">For more information, see the [support policy](https://www.microsoft.com/net/download/dotnet-core/2.0).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="6ccdb-280">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="6ccdb-280">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="6ccdb-281">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-281">Run the installer on the server.</span></span> <span data-ttu-id="6ccdb-282">从管理员命令提示符运行安装程序时，以下开关将可用：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-282">The following switches are available when running the installer from an administrator command prompt:</span></span>

   * <span data-ttu-id="6ccdb-283">`OPT_NO_ANCM=1` &ndash; 跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-283">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="6ccdb-284">`OPT_NO_RUNTIME=1` &ndash; 跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-284">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span>
   * <span data-ttu-id="6ccdb-285">`OPT_NO_SHAREDFX=1` &ndash; 跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-285">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span>
   * <span data-ttu-id="6ccdb-286">`OPT_NO_X86=1` &ndash; 跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-286">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="6ccdb-287">确定不会托管 32 位应用时，请使用此开关。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-287">Use this switch when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="6ccdb-288">如果有同时托管 32 位和 64 位应用的可能，请不要使用此开关并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-288">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this switch and install both runtimes.</span></span>
1. <span data-ttu-id="6ccdb-289">重启系统，或从命令提示符处依次执行 net stop was /y 和 net start w3svc。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-289">Restart the system or execute **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span> <span data-ttu-id="6ccdb-290">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-290">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="6ccdb-291">如果 Windows 托管捆绑包安装程序检测到 IIS 需要重置才能完成安装，则安装程序会重置 IIS。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-291">If the Windows Hosting Bundle installer detects that IIS requires a reset in order to complete installation, the installer resets IIS.</span></span> <span data-ttu-id="6ccdb-292">如果安装程序触发 IIS 重置，则会重新启动所有 IIS 应用池和网站。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-292">If the installer triggers an IIS reset, all of the IIS app pools and websites are restarted.</span></span>

> [!NOTE]
> <span data-ttu-id="6ccdb-293">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-293">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="6ccdb-294">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="6ccdb-294">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="6ccdb-295">使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-295">When deploying apps to servers with [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="6ccdb-296">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-296">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="6ccdb-297">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-297">The preferred method is to use WebPI.</span></span> <span data-ttu-id="6ccdb-298">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-298">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="6ccdb-299">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="6ccdb-299">Create the IIS site</span></span>

1. <span data-ttu-id="6ccdb-300">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-300">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="6ccdb-301">[目录结构](xref:host-and-deploy/directory-structure)主题中介绍了应用的部署布局。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-301">An app's deployment layout is described in the [Directory Structure](xref:host-and-deploy/directory-structure) topic.</span></span>

1. <span data-ttu-id="6ccdb-302">在“IIS 管理器”中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-302">In **IIS Manager**, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="6ccdb-303">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-303">Right-click the **Sites** folder.</span></span> <span data-ttu-id="6ccdb-304">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-304">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="6ccdb-305">提供网站名称，并将物理路径设置为应用的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-305">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="6ccdb-306">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-306">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="6ccdb-308">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-308">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="6ccdb-309">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-309">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="6ccdb-310">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-310">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="6ccdb-311">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-311">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="6ccdb-312">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-312">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="6ccdb-313">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-313">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="6ccdb-314">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-314">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="6ccdb-315">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-315">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="6ccdb-316">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-316">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="6ccdb-318">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-318">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="6ccdb-319">ASP.NET Core 不依赖加载桌面 CLR。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-319">ASP.NET Core doesn't rely on loading the desktop CLR.</span></span> <span data-ttu-id="6ccdb-320">将“.NET CLR 版本”设置为“无托管代码”为可选步骤。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-320">Setting the **.NET CLR version** to **No Managed Code** is optional.</span></span>

1. <span data-ttu-id="6ccdb-321">*ASP.NET Core 2.2 或更高版本*：对于使用[进程内托管模型](xref:fundamentals/servers/index#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-321">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="6ccdb-322">在 IIS 管理员的“应用程序池”的“操作”侧栏中，选择“设置应用程序池默认设置”或“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-322">In the **Actions** sidebar of IIS Manager's **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="6ccdb-323">找到“启用 32 位应用程序”并将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-323">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="6ccdb-324">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-324">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="6ccdb-325">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-325">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="6ccdb-326">如果将应用池的默认标识（“进程模型” > “标识”）从 ApplicationPoolIdentity 更改为另一标识，请验证新标识拥有所需的权限，可访问应用的文件夹、数据库和其他所需资源。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-326">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="6ccdb-327">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-327">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="6ccdb-328">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-328">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="6ccdb-329">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-329">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="6ccdb-330">部署应用</span><span class="sxs-lookup"><span data-stu-id="6ccdb-330">Deploy the app</span></span>

<span data-ttu-id="6ccdb-331">将应用部署到在托管系统上创建的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-331">Deploy the app to the folder created on the hosting system.</span></span> <span data-ttu-id="6ccdb-332">建议使用的部署机制是 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-332">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="6ccdb-333">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="6ccdb-333">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="6ccdb-334">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-334">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="6ccdb-335">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-335">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog.</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="6ccdb-337">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="6ccdb-337">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="6ccdb-338">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-338">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="6ccdb-339">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-339">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="6ccdb-340">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="6ccdb-340">Alternatives to Web Deploy</span></span>

<span data-ttu-id="6ccdb-341">有多种方法可将应用移动到托管系统，例如手动复制、Xcopy、Robocopy 或 PowerShell，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-341">Use any of several methods to move the app to the hosting system, such as manual copy, Xcopy, Robocopy, or PowerShell.</span></span>

<span data-ttu-id="6ccdb-342">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-342">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="6ccdb-343">浏览网站</span><span class="sxs-lookup"><span data-stu-id="6ccdb-343">Browse the website</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="6ccdb-345">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="6ccdb-345">Locked deployment files</span></span>

<span data-ttu-id="6ccdb-346">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-346">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="6ccdb-347">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-347">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="6ccdb-348">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-348">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="6ccdb-349">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-349">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="6ccdb-350">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-350">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="6ccdb-351">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-351">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="6ccdb-352">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-352">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="6ccdb-353">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-353">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="6ccdb-354">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-354">Use PowerShell to drop *app_offline.html* (requires PowerShell 5 or later):</span></span>

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="6ccdb-355">数据保护</span><span class="sxs-lookup"><span data-stu-id="6ccdb-355">Data protection</span></span>

<span data-ttu-id="6ccdb-356">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-356">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="6ccdb-357">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-357">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="6ccdb-358">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-358">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="6ccdb-359">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-359">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="6ccdb-360">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-360">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="6ccdb-361">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-361">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="6ccdb-362">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-362">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="6ccdb-363">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-363">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="6ccdb-364">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-364">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="6ccdb-365">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-365">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="6ccdb-366">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-366">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="6ccdb-367">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-367">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="6ccdb-368">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-368">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="6ccdb-369">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-369">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="6ccdb-370">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-370">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="6ccdb-371">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-371">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="6ccdb-372">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-372">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="6ccdb-373">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-373">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="6ccdb-374">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-374">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="6ccdb-375">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-375">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="6ccdb-376">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-376">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="6ccdb-377">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-377">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="6ccdb-378">此设置位于应用池“高级设置”下的“进程模型”部分。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-378">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="6ccdb-379">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-379">Set Load User Profile to `True`.</span></span> <span data-ttu-id="6ccdb-380">这会将密钥存储在用户配置文件目录下，并使用 DPAPI 和特定于用户帐户（用于应用池）的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-380">This stores keys under the user profile directory and protects them using DPAPI with a key specific to the user account used by the app pool.</span></span>

* <span data-ttu-id="6ccdb-381">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-381">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="6ccdb-382">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-382">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="6ccdb-383">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-383">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="6ccdb-384">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-384">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="6ccdb-385">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-385">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="6ccdb-386">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-386">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="6ccdb-387">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-387">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="6ccdb-388">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-388">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="6ccdb-389">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="6ccdb-389">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="6ccdb-390">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-390">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="6ccdb-391">有关更多信息，请参见<xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-391">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="6ccdb-392">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="6ccdb-392">Virtual Directories</span></span>

<span data-ttu-id="6ccdb-393">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-393">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="6ccdb-394">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-394">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="6ccdb-395">子应用程序</span><span class="sxs-lookup"><span data-stu-id="6ccdb-395">Sub-applications</span></span>

<span data-ttu-id="6ccdb-396">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-396">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="6ccdb-397">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-397">The sub-app's path becomes part of the root app's URL.</span></span>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6ccdb-398">子应用不应将 ASP.NET Core 模块作为处理程序包含在其中。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-398">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="6ccdb-399">如果在子应用的 web.config 文件中将该模块添加为处理程序，则在尝试浏览子应用时会收到“500.19 内部服务器错误”，即引用错误的配置文件。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-399">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="6ccdb-400">以下示例显示 ASP.NET Core 子应用的已发布 web.config 文件：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-400">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="6ccdb-401">在 ASP.NET Core 应用下托管非 ASP.NET Core 子应用时，需在子应用的 web.config 文件中显式删除继承的处理程序：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-401">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

::: moniker-end

<span data-ttu-id="6ccdb-402">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-402">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="6ccdb-403">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-403">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="6ccdb-404">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-404">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="6ccdb-405">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-405">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="6ccdb-406">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-406">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="6ccdb-407">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-407">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="6ccdb-408">根应用的静态文件中间件试图从根应用的 [webroot](xref:fundamentals/index#web-root-webroot) 提供资产，这会导致“404 - 找不到”响应，除非可从根应用获得此静态资产。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-408">The root app's Static File Middleware attempts to serve the asset from the root app's [webroot](xref:fundamentals/index#web-root-webroot), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="6ccdb-409">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-409">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="6ccdb-410">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-410">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="6ccdb-411">将“.NET CLR 版本”设置为“无托管代码”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-411">Set the **.NET CLR Version** to **No Managed Code**.</span></span>

1. <span data-ttu-id="6ccdb-412">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-412">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="6ccdb-413">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-413">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="6ccdb-414">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-414">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="6ccdb-415">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-415">Select **OK**.</span></span>

<span data-ttu-id="6ccdb-416">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-416">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="6ccdb-417">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module> 和 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-417">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module> and <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="6ccdb-418">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="6ccdb-418">Configuration of IIS with web.config</span></span>

<span data-ttu-id="6ccdb-419">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-419">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="6ccdb-420">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-420">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="6ccdb-421">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-421">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="6ccdb-422">有关详细信息，请参阅 [\<system.webServer> 的配置参考](/iis/configuration/system.webServer/)、[ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module)和[使用 ASP.NET Core 的 IIS 模块](xref:host-and-deploy/iis/modules)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-422">For more information, see the [configuration reference for \<system.webServer>](/iis/configuration/system.webServer/), [ASP.NET Core Module Configuration Reference](xref:host-and-deploy/aspnet-core-module), and [IIS Modules with ASP.NET Core](xref:host-and-deploy/iis/modules).</span></span> <span data-ttu-id="6ccdb-423">若要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档的[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题中的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-423">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="6ccdb-424">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="6ccdb-424">Configuration sections of web.config</span></span>

<span data-ttu-id="6ccdb-425">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-425">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="6ccdb-426">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-426">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="6ccdb-427">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-427">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="6ccdb-428">应用程序池</span><span class="sxs-lookup"><span data-stu-id="6ccdb-428">Application Pools</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6ccdb-429">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-429">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="6ccdb-430">进程内托管 &ndash; 应用都需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-430">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="6ccdb-431">进程外托管 &ndash; 建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-431">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="6ccdb-432">IIS“添加网站”对话框默认为每应用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-432">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="6ccdb-433">提供了站点名称时，该文本会自动传输到“应用程序池”文本框。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-433">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="6ccdb-434">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-434">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6ccdb-435">在服务器上托管多个网站时，建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-435">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="6ccdb-436">IIS“添加网站”对话框默认执行此配置。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-436">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="6ccdb-437">提供了站点名称时，该文本会自动传输到“应用程序池”文本框。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-437">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="6ccdb-438">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-438">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

## <a name="application-pool-identity"></a><span data-ttu-id="6ccdb-439">应用程序池标识</span><span class="sxs-lookup"><span data-stu-id="6ccdb-439">Application Pool Identity</span></span>

<span data-ttu-id="6ccdb-440">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-440">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="6ccdb-441">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-441">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="6ccdb-442">在 IIS 管理控制台中，确保应用池“高级设置”下的“标识”设置为使用 ApplicationPoolIdentity：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-442">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="6ccdb-444">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-444">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="6ccdb-445">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-445">Resources can be secured using this identity.</span></span> <span data-ttu-id="6ccdb-446">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-446">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="6ccdb-447">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-447">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="6ccdb-448">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-448">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="6ccdb-449">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-449">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="6ccdb-450">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-450">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="6ccdb-451">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-451">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="6ccdb-452">在“输入要选择的对象名称”区域中输入**IIS AppPool\\<app_pool_name>**。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-452">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="6ccdb-453">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-453">Select the **Check Names** button.</span></span> <span data-ttu-id="6ccdb-454">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-454">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="6ccdb-455">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-455">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="6ccdb-456">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-456">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="6ccdb-457">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-457">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="6ccdb-459">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-459">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="6ccdb-461">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-461">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="6ccdb-462">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-462">Provide additional permissions as needed.</span></span>

<span data-ttu-id="6ccdb-463">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-463">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="6ccdb-464">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-464">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="6ccdb-465">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-465">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="6ccdb-466">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="6ccdb-466">HTTP/2 support</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6ccdb-467">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-467">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="6ccdb-468">进程内</span><span class="sxs-lookup"><span data-stu-id="6ccdb-468">In-process</span></span>
  * <span data-ttu-id="6ccdb-469">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6ccdb-469">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="6ccdb-470">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="6ccdb-470">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="6ccdb-471">进程外</span><span class="sxs-lookup"><span data-stu-id="6ccdb-471">Out-of-process</span></span>
  * <span data-ttu-id="6ccdb-472">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6ccdb-472">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="6ccdb-473">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-473">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="6ccdb-474">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="6ccdb-474">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6ccdb-475">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-475">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="6ccdb-476">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-476">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="6ccdb-477">有关进程内和进程外托管模型的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module> 主题和 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-477">For more information on the in-process and out-of-process hosting models, see the <xref:host-and-deploy/aspnet-core-module> topic and the <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6ccdb-478">满足以下基本要求的进程外部署支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="6ccdb-478">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="6ccdb-479">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6ccdb-479">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="6ccdb-480">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-480">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="6ccdb-481">目标框架：不适用于进程外部署，因为 HTTP/2 连接完全由 IIS 处理。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-481">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="6ccdb-482">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="6ccdb-482">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6ccdb-483">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-483">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="6ccdb-484">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-484">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="6ccdb-485">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-485">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="6ccdb-486">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-486">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="6ccdb-487">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="6ccdb-487">CORS preflight requests</span></span>

<span data-ttu-id="6ccdb-488">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-488">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="6ccdb-489">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-489">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="6ccdb-490">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-490">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="6ccdb-491">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="6ccdb-491">Deployment resources for IIS administrators</span></span>

<span data-ttu-id="6ccdb-492">在 IIS 文档中深入了解 IIS。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-492">Learn about IIS in-depth in the IIS documentation.</span></span>  
[<span data-ttu-id="6ccdb-493">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="6ccdb-493">IIS documentation</span></span>](/iis)

<span data-ttu-id="6ccdb-494">了解 .NET Core 应用部署模型。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-494">Learn about .NET Core app deployment models.</span></span>  
[<span data-ttu-id="6ccdb-495">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="6ccdb-495">.NET Core application deployment</span></span>](/dotnet/core/deploying/)

<span data-ttu-id="6ccdb-496">了解 ASP.NET Core 模块如何使 Kestrel Web 服务器将 IIS 或 IIS Express 用作反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-496">Learn how the ASP.NET Core Module allows the Kestrel web server to use IIS or IIS Express as a reverse proxy server.</span></span>  
[<span data-ttu-id="6ccdb-497">ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="6ccdb-497">ASP.NET Core Module</span></span>](xref:host-and-deploy/aspnet-core-module)

<span data-ttu-id="6ccdb-498">了解如何配置 ASP.NET Core 模块以托管 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-498">Learn how to configure the ASP.NET Core Module for hosting ASP.NET Core apps.</span></span>  
[<span data-ttu-id="6ccdb-499">ASP.NET Core 模块配置参考</span><span class="sxs-lookup"><span data-stu-id="6ccdb-499">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)

<span data-ttu-id="6ccdb-500">了解已发布的 ASP.NET Core 应用的目录结构。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-500">Learn about the directory structure of published ASP.NET Core apps.</span></span>  
[<span data-ttu-id="6ccdb-501">目录结构</span><span class="sxs-lookup"><span data-stu-id="6ccdb-501">Directory structure</span></span>](xref:host-and-deploy/directory-structure)

<span data-ttu-id="6ccdb-502">了解适用于 ASP.NET Core 应用的活动和非活动 IIS 模块以及如何管理 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-502">Discover active and inactive IIS modules for ASP.NET Core apps and how to manage IIS modules.</span></span>  
[<span data-ttu-id="6ccdb-503">IIS 模块</span><span class="sxs-lookup"><span data-stu-id="6ccdb-503">IIS modules</span></span>](xref:host-and-deploy/iis/troubleshoot)

<span data-ttu-id="6ccdb-504">了解如何诊断 ASP.NET Core 应用的 IIS 部署的问题。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-504">Learn how to diagnose problems with IIS deployments of ASP.NET Core apps.</span></span>  
[<span data-ttu-id="6ccdb-505">疑难解答</span><span class="sxs-lookup"><span data-stu-id="6ccdb-505">Troubleshoot</span></span>](xref:host-and-deploy/iis/troubleshoot)

<span data-ttu-id="6ccdb-506">了解在 IIS 上托管 ASP.NET Core 应用的常见错误。</span><span class="sxs-lookup"><span data-stu-id="6ccdb-506">Distinguish common errors when hosting ASP.NET Core apps on IIS.</span></span>  
[<span data-ttu-id="6ccdb-507">Azure 应用服务和 IIS 的常见错误参考</span><span class="sxs-lookup"><span data-stu-id="6ccdb-507">Common errors reference for Azure App Service and IIS</span></span>](xref:host-and-deploy/azure-iis-errors-reference)

## <a name="additional-resources"></a><span data-ttu-id="6ccdb-508">其他资源</span><span class="sxs-lookup"><span data-stu-id="6ccdb-508">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="6ccdb-509">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="6ccdb-509">Introduction to ASP.NET Core</span></span>](xref:index)
* [<span data-ttu-id="6ccdb-510">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="6ccdb-510">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="6ccdb-511">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="6ccdb-511">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="6ccdb-512">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6ccdb-512">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
