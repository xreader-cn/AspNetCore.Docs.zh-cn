---
title: ASP.NET Core 中的 .NET 通用主机
author: rick-anderson
description: 使用 ASP.NET Core 应用中的 .NET Core 通用主机。  通用主机负责应用启动和生存期管理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: d3de81ce7248372279b423da865513ee5db73c79
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762316"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="8b74f-104">ASP.NET Core 中的 .NET 通用主机</span><span class="sxs-lookup"><span data-stu-id="8b74f-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8b74f-105">ASP.NET Core 模板会创建一个 .NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="8b74f-106">本主题介绍如何使用 ASP.NET Core 中的 .NET 通用主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="8b74f-107">若要了解如何使用控制台应用中的 .NET 通用主机，请参阅 [.NET 通用主机](/dotnet/core/extensions/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="8b74f-108">主机定义</span><span class="sxs-lookup"><span data-stu-id="8b74f-108">Host definition</span></span>

<span data-ttu-id="8b74f-109">主机是封装应用资源的对象，例如：</span><span class="sxs-lookup"><span data-stu-id="8b74f-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="8b74f-110">依赖关系注入 (DI)</span><span class="sxs-lookup"><span data-stu-id="8b74f-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="8b74f-111">Logging</span><span class="sxs-lookup"><span data-stu-id="8b74f-111">Logging</span></span>
* <span data-ttu-id="8b74f-112">Configuration</span><span class="sxs-lookup"><span data-stu-id="8b74f-112">Configuration</span></span>
* <span data-ttu-id="8b74f-113">`IHostedService` 实现</span><span class="sxs-lookup"><span data-stu-id="8b74f-113">`IHostedService` implementations</span></span>

<span data-ttu-id="8b74f-114">当主机启动时，它将对在托管服务的服务容器集合中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的每个实现调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="8b74f-115">在 web 应用中，其中一个 `IHostedService` 实现是启动 [HTTP 服务器实现](xref:fundamentals/index#servers)的 web 服务。</span><span class="sxs-lookup"><span data-stu-id="8b74f-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="8b74f-116">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8b74f-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="8b74f-117">设置主机</span><span class="sxs-lookup"><span data-stu-id="8b74f-117">Set up a host</span></span>

<span data-ttu-id="8b74f-118">主机通常由 `Program` 类中的代码配置、生成和运行。</span><span class="sxs-lookup"><span data-stu-id="8b74f-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="8b74f-119">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="8b74f-119">The `Main` method:</span></span>

* <span data-ttu-id="8b74f-120">调用 `CreateHostBuilder` 方法以创建和配置生成器对象。</span><span class="sxs-lookup"><span data-stu-id="8b74f-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="8b74f-121">对生成器对象调用 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b74f-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="8b74f-122">ASP.NET Core Web 模板会生成以下代码来创建一个主机：</span><span class="sxs-lookup"><span data-stu-id="8b74f-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="8b74f-123">以下代码会使用添加到 DI 容器中的 `IHostedService` 实现创建一个非 HTTP 工作负载。</span><span class="sxs-lookup"><span data-stu-id="8b74f-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="8b74f-124">对于 HTTP 工作负荷，`Main` 方法相同，但 `CreateHostBuilder` 调用 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="8b74f-125">如果应用使用 Entity Framework Core，不要更改 `CreateHostBuilder` 方法的名称或签名。</span><span class="sxs-lookup"><span data-stu-id="8b74f-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="8b74f-126">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)应查找一个无需运行应用即可配置主机的 `CreateHostBuilder` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b74f-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="8b74f-127">有关详细信息，请参阅[设计时 DbContext 创建](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="8b74f-128">默认生成器设置</span><span class="sxs-lookup"><span data-stu-id="8b74f-128">Default builder settings</span></span>

<span data-ttu-id="8b74f-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 方法：</span><span class="sxs-lookup"><span data-stu-id="8b74f-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="8b74f-130">将[内容根目录](xref:fundamentals/index#content-root)设置为由 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的路径。</span><span class="sxs-lookup"><span data-stu-id="8b74f-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="8b74f-131">通过以下项加载主机配置：</span><span class="sxs-lookup"><span data-stu-id="8b74f-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="8b74f-132">前缀为 `DOTNET_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b74f-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="8b74f-133">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="8b74f-133">Command-line arguments.</span></span>
* <span data-ttu-id="8b74f-134">通过以下对象加载应用配置：</span><span class="sxs-lookup"><span data-stu-id="8b74f-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="8b74f-135">appsettings.json。</span><span class="sxs-lookup"><span data-stu-id="8b74f-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="8b74f-136">appsettings.{Environment}.json。</span><span class="sxs-lookup"><span data-stu-id="8b74f-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="8b74f-137">[密钥管理器](xref:security/app-secrets) 当应用在 `Development` 环境中运行时。</span><span class="sxs-lookup"><span data-stu-id="8b74f-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="8b74f-138">环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b74f-138">Environment variables.</span></span>
  * <span data-ttu-id="8b74f-139">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="8b74f-139">Command-line arguments.</span></span>
* <span data-ttu-id="8b74f-140">添加以下[日志记录](xref:fundamentals/logging/index)提供程序：</span><span class="sxs-lookup"><span data-stu-id="8b74f-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="8b74f-141">控制台</span><span class="sxs-lookup"><span data-stu-id="8b74f-141">Console</span></span>
  * <span data-ttu-id="8b74f-142">调试</span><span class="sxs-lookup"><span data-stu-id="8b74f-142">Debug</span></span>
  * <span data-ttu-id="8b74f-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="8b74f-143">EventSource</span></span>
  * <span data-ttu-id="8b74f-144">EventLog（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="8b74f-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="8b74f-145">当环境为“开发”时，启用[范围验证](xref:fundamentals/dependency-injection#scope-validation)和[依赖关系验证](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="8b74f-146">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="8b74f-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="8b74f-147">从前缀为 `ASPNETCORE_` 的环境变量加载主机配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="8b74f-148">使用应用的托管配置提供程序将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器设置为 web 服务器并对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="8b74f-149">有关 Kestrel 服务器默认选项，请参阅 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="8b74f-150">添加[主机筛选中间件](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="8b74f-151">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 等于 `true`，则添加[转接头中间件](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="8b74f-152">支持 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="8b74f-152">Enables IIS integration.</span></span> <span data-ttu-id="8b74f-153">有关 IIS 默认选项，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="8b74f-154">本文中后面的[所有应用类型的设置](#settings-for-all-app-types)和[ web 应用的设置](#settings-for-web-apps)部分介绍如何替代默认生成器设置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="8b74f-155">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="8b74f-155">Framework-provided services</span></span>

<span data-ttu-id="8b74f-156">自动注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="8b74f-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="8b74f-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8b74f-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="8b74f-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="8b74f-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="8b74f-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="8b74f-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="8b74f-160">有关框架提供的服务的详细信息，请参阅 <xref:fundamentals/dependency-injection#framework-provided-services>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="8b74f-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8b74f-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="8b74f-162">将 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>（以前称为 `IApplicationLifetime`）服务注入任何类以处理启动后和正常关闭任务。</span><span class="sxs-lookup"><span data-stu-id="8b74f-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="8b74f-163">接口上的三个属性是用于注册应用启动和应用停止事件处理程序方法的取消令牌。</span><span class="sxs-lookup"><span data-stu-id="8b74f-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="8b74f-164">该接口还包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b74f-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="8b74f-165">以下示例是注册 `IHostApplicationLifetime` 事件的 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="8b74f-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="8b74f-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="8b74f-166">IHostLifetime</span></span>

<span data-ttu-id="8b74f-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 实现控制主机何时启动和何时停止。</span><span class="sxs-lookup"><span data-stu-id="8b74f-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="8b74f-168">使用了已注册的最后一个实现。</span><span class="sxs-lookup"><span data-stu-id="8b74f-168">The last implementation registered is used.</span></span>

<span data-ttu-id="8b74f-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是默认的 `IHostLifetime` 实现。</span><span class="sxs-lookup"><span data-stu-id="8b74f-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="8b74f-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="8b74f-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="8b74f-171">侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="8b74f-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="8b74f-172">解除阻止 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="8b74f-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="8b74f-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="8b74f-173">IHostEnvironment</span></span>

<span data-ttu-id="8b74f-174">将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 服务注册到一个类，获取关于以下设置的信息：</span><span class="sxs-lookup"><span data-stu-id="8b74f-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="8b74f-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="8b74f-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="8b74f-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="8b74f-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="8b74f-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="8b74f-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="8b74f-178">Web 应用实现 `IWebHostEnvironment` 接口，该接口继承 `IHostEnvironment` 并添加 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="8b74f-179">主机配置</span><span class="sxs-lookup"><span data-stu-id="8b74f-179">Host configuration</span></span>

<span data-ttu-id="8b74f-180">主机配置用于 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 实现的属性。</span><span class="sxs-lookup"><span data-stu-id="8b74f-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="8b74f-181">主机配置可以从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) 获取。</span><span class="sxs-lookup"><span data-stu-id="8b74f-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="8b74f-182">在 `ConfigureAppConfiguration` 后，`HostBuilderContext.Configuration` 被替换为应用配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="8b74f-183">若要添加主机配置，请对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="8b74f-184">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="8b74f-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="8b74f-185">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="8b74f-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="8b74f-186">`CreateDefaultBuilder` 包含前缀为 `DOTNET_` 的环境变量提供程序和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="8b74f-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b74f-187">对于 web 应用程序，添加前缀为 `ASPNETCORE_` 的环境变量提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b74f-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="8b74f-188">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="8b74f-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="8b74f-189">例如，`ASPNETCORE_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="8b74f-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="8b74f-190">以下示例创建主机配置：</span><span class="sxs-lookup"><span data-stu-id="8b74f-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="8b74f-191">应用配置</span><span class="sxs-lookup"><span data-stu-id="8b74f-191">App configuration</span></span>

<span data-ttu-id="8b74f-192">通过对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="8b74f-193">可多次调用 `ConfigureAppConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="8b74f-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="8b74f-194">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="8b74f-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="8b74f-195">由 `ConfigureAppConfiguration` 创建的配置可以通过 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 获取以用于后续操作，也可以通过 DI 作为服务获取。</span><span class="sxs-lookup"><span data-stu-id="8b74f-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="8b74f-196">主机配置也会添加到应用配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="8b74f-197">有关详细信息，请参阅 [ ASP.NET Core 中的配置](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="8b74f-198">适用于所有应用类型的设置</span><span class="sxs-lookup"><span data-stu-id="8b74f-198">Settings for all app types</span></span>

<span data-ttu-id="8b74f-199">本部分列出了适用于 HTTP 和非 HTTP 工作负荷的主机设置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="8b74f-200">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="8b74f-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="8b74f-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="8b74f-201">ApplicationName</span></span>

<span data-ttu-id="8b74f-202">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="8b74f-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="8b74f-203">键：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="8b74f-203">**Key**: `applicationName`</span></span>  
<span data-ttu-id="8b74f-204">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-204">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-205">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="8b74f-205">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="8b74f-206">**环境变量**：`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="8b74f-206">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="8b74f-207">要设置此值，请使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b74f-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="8b74f-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="8b74f-208">ContentRoot</span></span>

<span data-ttu-id="8b74f-209">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 属性决定主机从什么位置开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="8b74f-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="8b74f-210">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="8b74f-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="8b74f-211">键：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="8b74f-211">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="8b74f-212">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-212">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-213">**默认**：应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8b74f-213">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="8b74f-214">**环境变量**：`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="8b74f-214">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="8b74f-215">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="8b74f-216">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="8b74f-216">For more information, see:</span></span>

* [<span data-ttu-id="8b74f-217">基础知识：内容根目录</span><span class="sxs-lookup"><span data-stu-id="8b74f-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="8b74f-218">WebRoot</span><span class="sxs-lookup"><span data-stu-id="8b74f-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="8b74f-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="8b74f-219">EnvironmentName</span></span>

<span data-ttu-id="8b74f-220">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 属性可以设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="8b74f-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="8b74f-221">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="8b74f-222">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="8b74f-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="8b74f-223">键：`environment`</span><span class="sxs-lookup"><span data-stu-id="8b74f-223">**Key**: `environment`</span></span>  
<span data-ttu-id="8b74f-224">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-224">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-225">**默认**：`Production`</span><span class="sxs-lookup"><span data-stu-id="8b74f-225">**Default**: `Production`</span></span>  
<span data-ttu-id="8b74f-226">**环境变量**：`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="8b74f-226">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="8b74f-227">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="8b74f-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="8b74f-228">ShutdownTimeout</span></span>

<span data-ttu-id="8b74f-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时。</span><span class="sxs-lookup"><span data-stu-id="8b74f-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="8b74f-230">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="8b74f-230">The default value is five seconds.</span></span>  <span data-ttu-id="8b74f-231">在超时时间段中，主机：</span><span class="sxs-lookup"><span data-stu-id="8b74f-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="8b74f-232">触发 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="8b74f-233">尝试停止托管服务，对服务停止失败的错误进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="8b74f-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="8b74f-234">如果在所有托管服务停止之前就达到了超时时间，则会在应用关闭时会终止剩余的所有活动的服务。</span><span class="sxs-lookup"><span data-stu-id="8b74f-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="8b74f-235">即使没有完成处理工作，服务也会停止。</span><span class="sxs-lookup"><span data-stu-id="8b74f-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="8b74f-236">如果停止服务需要额外的时间，请增加超时时间。</span><span class="sxs-lookup"><span data-stu-id="8b74f-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="8b74f-237">键：`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="8b74f-237">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="8b74f-238">类型：`int`</span><span class="sxs-lookup"><span data-stu-id="8b74f-238">**Type**: `int`</span></span>  
<span data-ttu-id="8b74f-239">**默认**：5 秒</span><span class="sxs-lookup"><span data-stu-id="8b74f-239">**Default**: 5 seconds</span></span>  
<span data-ttu-id="8b74f-240">**环境变量**：`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="8b74f-240">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="8b74f-241">若要设置此值，请使用环境变量或配置 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="8b74f-242">以下示例将超时设置为 20 秒：</span><span class="sxs-lookup"><span data-stu-id="8b74f-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="8b74f-243">禁用“在更改时重载应用配置”</span><span class="sxs-lookup"><span data-stu-id="8b74f-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="8b74f-244">[默认情况下](xref:fundamentals/configuration/index#default)，appsettings.json 和 appsettings.{Environment}.json 会在文件更改时重载 。</span><span class="sxs-lookup"><span data-stu-id="8b74f-244">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="8b74f-245">若要在 ASP.NET Core 5.0 或更高版本中禁用此重载行为，请将 `hostBuilder:reloadConfigOnChange` 键设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="8b74f-246">键：`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="8b74f-246">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="8b74f-247">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="8b74f-247">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8b74f-248">**默认**：`true`</span><span class="sxs-lookup"><span data-stu-id="8b74f-248">**Default**: `true`</span></span>  
<span data-ttu-id="8b74f-249">命令行参数：`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="8b74f-249">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="8b74f-250">**环境变量**：`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="8b74f-250">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="8b74f-251">所有平台上的环境变量分层键都不支持冒号 (`:`) 分隔符。</span><span class="sxs-lookup"><span data-stu-id="8b74f-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="8b74f-252">有关详细信息，请参阅[环境变量](xref:fundamentals/configuration/index#environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="8b74f-253">适用于 Web 应用的设置</span><span class="sxs-lookup"><span data-stu-id="8b74f-253">Settings for web apps</span></span>

<span data-ttu-id="8b74f-254">一些主机设置仅适用于 HTTP 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="8b74f-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="8b74f-255">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="8b74f-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="8b74f-256">`IWebHostBuilder` 上的扩展方法适用于这些设置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="8b74f-257">显示如何调用扩展方法的示例代码假定 `webBuilder` 是 `IWebHostBuilder` 的实例，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="8b74f-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="8b74f-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="8b74f-258">CaptureStartupErrors</span></span>

<span data-ttu-id="8b74f-259">当 `false` 时，启动期间出错导致主机退出。</span><span class="sxs-lookup"><span data-stu-id="8b74f-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="8b74f-260">当 `true` 时，主机在启动期间捕获异常并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="8b74f-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="8b74f-261">键：`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="8b74f-261">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="8b74f-262">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="8b74f-262">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8b74f-263">**默认**：默认为 `false`，除非应用使用 Kestrel 在 IIS 后方运行，其中默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-263">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="8b74f-264">**环境变量**：`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="8b74f-264">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="8b74f-265">若要设置此值，使用配置或调用 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="8b74f-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="8b74f-266">DetailedErrors</span></span>

<span data-ttu-id="8b74f-267">如果启用，或环境为 `Development`，应用会捕获详细错误。</span><span class="sxs-lookup"><span data-stu-id="8b74f-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="8b74f-268">键：`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="8b74f-268">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="8b74f-269">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="8b74f-269">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8b74f-270">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="8b74f-270">**Default**: `false`</span></span>  
<span data-ttu-id="8b74f-271">**环境变量**：`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="8b74f-271">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="8b74f-272">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="8b74f-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="8b74f-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="8b74f-274">承载启动程序集的以分号分隔的字符串在启动时加载。</span><span class="sxs-lookup"><span data-stu-id="8b74f-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="8b74f-275">虽然配置值默认为空字符串，但是承载启动程序集会始终包含应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="8b74f-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="8b74f-276">提供承载启动程序集时，当应用在启动过程中生成其公用服务时将它们添加到应用的程序集加载。</span><span class="sxs-lookup"><span data-stu-id="8b74f-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="8b74f-277">键：`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="8b74f-277">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="8b74f-278">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-278">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-279">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="8b74f-279">**Default**: Empty string</span></span>  
<span data-ttu-id="8b74f-280">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8b74f-280">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="8b74f-281">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="8b74f-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="8b74f-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="8b74f-283">承载启动程序集的以分号分隔的字符串在启动时排除。</span><span class="sxs-lookup"><span data-stu-id="8b74f-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="8b74f-284">键：`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="8b74f-284">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="8b74f-285">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-285">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-286">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="8b74f-286">**Default**: Empty string</span></span>  
<span data-ttu-id="8b74f-287">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8b74f-287">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="8b74f-288">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="8b74f-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="8b74f-289">HTTPS_Port</span></span>

<span data-ttu-id="8b74f-290">HTTPS 重定向端口。</span><span class="sxs-lookup"><span data-stu-id="8b74f-290">The HTTPS redirect port.</span></span> <span data-ttu-id="8b74f-291">用于[强制实施 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="8b74f-292">键：`https_port`</span><span class="sxs-lookup"><span data-stu-id="8b74f-292">**Key**: `https_port`</span></span>  
<span data-ttu-id="8b74f-293">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-293">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-294">**默认**：未设置默认值。</span><span class="sxs-lookup"><span data-stu-id="8b74f-294">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="8b74f-295">**环境变量**：`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="8b74f-295">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="8b74f-296">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="8b74f-297">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="8b74f-297">PreferHostingUrls</span></span>

<span data-ttu-id="8b74f-298">指示主机是否应该侦听使用 `IWebHostBuilder` 配置的 URL，而不是使用 `IServer` 实现配置的 URL。</span><span class="sxs-lookup"><span data-stu-id="8b74f-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="8b74f-299">键：`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="8b74f-299">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="8b74f-300">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="8b74f-300">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8b74f-301">**默认**：`true`</span><span class="sxs-lookup"><span data-stu-id="8b74f-301">**Default**: `true`</span></span>  
<span data-ttu-id="8b74f-302">**环境变量**：`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="8b74f-302">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="8b74f-303">若要设置此值，请使用环境变量或调用 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="8b74f-304">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="8b74f-304">PreventHostingStartup</span></span>

<span data-ttu-id="8b74f-305">阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="8b74f-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="8b74f-306">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="8b74f-307">键：`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="8b74f-307">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="8b74f-308">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="8b74f-308">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8b74f-309">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="8b74f-309">**Default**: `false`</span></span>  
<span data-ttu-id="8b74f-310">**环境变量**：`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="8b74f-310">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="8b74f-311">若要设置此值，请使用环境变量或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="8b74f-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="8b74f-312">StartupAssembly</span></span>

<span data-ttu-id="8b74f-313">要搜索 `Startup` 类的程序集。</span><span class="sxs-lookup"><span data-stu-id="8b74f-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="8b74f-314">键：`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="8b74f-314">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="8b74f-315">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-315">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-316">**默认**：应用的程序集</span><span class="sxs-lookup"><span data-stu-id="8b74f-316">**Default**: The app's assembly</span></span>  
<span data-ttu-id="8b74f-317">**环境变量**：`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="8b74f-317">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="8b74f-318">若要设置此值，请使用环境变量或调用 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="8b74f-319">`UseStartup` 可以采用程序集名称 (`string`) 或类型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="8b74f-320">如果调用多个 `UseStartup` 方法，优先选择最后一个方法。</span><span class="sxs-lookup"><span data-stu-id="8b74f-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="8b74f-321">URL</span><span class="sxs-lookup"><span data-stu-id="8b74f-321">URLs</span></span>

<span data-ttu-id="8b74f-322">IP 地址或主机地址的分号分隔列表，其中包含服务器应针对请求侦听的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="8b74f-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="8b74f-323">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="8b74f-324">使用“\*”指示服务器应针对请求侦听的使用特定端口和协议（例如 `http://*:5000`）的 IP 地址或主机名。</span><span class="sxs-lookup"><span data-stu-id="8b74f-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="8b74f-325">协议（`http://` 或 `https://`）必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="8b74f-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="8b74f-326">不同的服务器支持的格式有所不同。</span><span class="sxs-lookup"><span data-stu-id="8b74f-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="8b74f-327">键：`urls`</span><span class="sxs-lookup"><span data-stu-id="8b74f-327">**Key**: `urls`</span></span>  
<span data-ttu-id="8b74f-328">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-328">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-329">**默认值**：`http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="8b74f-329">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="8b74f-330">**环境变量**：`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="8b74f-330">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="8b74f-331">若要设置此值，请使用环境变量或调用 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="8b74f-332">Kestrel 具有自己的终结点配置 API。</span><span class="sxs-lookup"><span data-stu-id="8b74f-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="8b74f-333">有关详细信息，请参阅 <xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="8b74f-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="8b74f-334">WebRoot</span></span>

<span data-ttu-id="8b74f-335">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) 属性可确定应用静态资产的相对路径。</span><span class="sxs-lookup"><span data-stu-id="8b74f-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="8b74f-336">如果该路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b74f-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="8b74f-337">键：`webroot`</span><span class="sxs-lookup"><span data-stu-id="8b74f-337">**Key**: `webroot`</span></span>  
<span data-ttu-id="8b74f-338">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-338">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-339">**默认**：默认值为 `wwwroot`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-339">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="8b74f-340">{content root}/wwwroot 的路径必须存在。</span><span class="sxs-lookup"><span data-stu-id="8b74f-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="8b74f-341">**环境变量**：`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="8b74f-341">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="8b74f-342">若要设置此值，请使用环境变量或对 `IWebHostBuilder` 调用 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="8b74f-343">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="8b74f-343">For more information, see:</span></span>

* [<span data-ttu-id="8b74f-344">基础知识：Web 根目录</span><span class="sxs-lookup"><span data-stu-id="8b74f-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="8b74f-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="8b74f-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="8b74f-346">管理主机生存期</span><span class="sxs-lookup"><span data-stu-id="8b74f-346">Manage the host lifetime</span></span>

<span data-ttu-id="8b74f-347">对生成的 <xref:Microsoft.Extensions.Hosting.IHost> 实现调用方法，以启动和停止应用。</span><span class="sxs-lookup"><span data-stu-id="8b74f-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="8b74f-348">这些方法会影响所有在服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="8b74f-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="8b74f-349">运行</span><span class="sxs-lookup"><span data-stu-id="8b74f-349">Run</span></span>

<span data-ttu-id="8b74f-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="8b74f-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-351">RunAsync</span></span>

<span data-ttu-id="8b74f-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="8b74f-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-353">RunConsoleAsync</span></span>

<span data-ttu-id="8b74f-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="8b74f-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="8b74f-355">Start</span><span class="sxs-lookup"><span data-stu-id="8b74f-355">Start</span></span>

<span data-ttu-id="8b74f-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="8b74f-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-357">StartAsync</span></span>

<span data-ttu-id="8b74f-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动主机并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="8b74f-359">在 `StartAsync` 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="8b74f-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="8b74f-360">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="8b74f-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="8b74f-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-361">StopAsync</span></span>

<span data-ttu-id="8b74f-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="8b74f-363">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="8b74f-363">WaitForShutdown</span></span>

<span data-ttu-id="8b74f-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 阻止调用线程，直到 IHostLifetime 触发关闭，例如通过 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM。</span><span class="sxs-lookup"><span data-stu-id="8b74f-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="8b74f-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="8b74f-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="8b74f-367">外部控件</span><span class="sxs-lookup"><span data-stu-id="8b74f-367">External control</span></span>

<span data-ttu-id="8b74f-368">使用可从外部调用的方法，能够实现对主机生存期的直接控制：</span><span class="sxs-lookup"><span data-stu-id="8b74f-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="8b74f-369">ASP.NET Core 模板会创建一个 .NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="8b74f-370">本主题介绍如何使用 ASP.NET Core 中的 .NET 通用主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="8b74f-371">若要了解如何使用控制台应用中的 .NET 通用主机，请参阅 [.NET 通用主机](/dotnet/core/extensions/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="8b74f-372">主机定义</span><span class="sxs-lookup"><span data-stu-id="8b74f-372">Host definition</span></span>

<span data-ttu-id="8b74f-373">主机是封装应用资源的对象，例如：</span><span class="sxs-lookup"><span data-stu-id="8b74f-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="8b74f-374">依赖关系注入 (DI)</span><span class="sxs-lookup"><span data-stu-id="8b74f-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="8b74f-375">Logging</span><span class="sxs-lookup"><span data-stu-id="8b74f-375">Logging</span></span>
* <span data-ttu-id="8b74f-376">Configuration</span><span class="sxs-lookup"><span data-stu-id="8b74f-376">Configuration</span></span>
* <span data-ttu-id="8b74f-377">`IHostedService` 实现</span><span class="sxs-lookup"><span data-stu-id="8b74f-377">`IHostedService` implementations</span></span>

<span data-ttu-id="8b74f-378">当主机启动时，它将对在托管服务的服务容器集合中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的每个实现调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="8b74f-379">在 web 应用中，其中一个 `IHostedService` 实现是启动 [HTTP 服务器实现](xref:fundamentals/index#servers)的 web 服务。</span><span class="sxs-lookup"><span data-stu-id="8b74f-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="8b74f-380">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8b74f-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="8b74f-381">设置主机</span><span class="sxs-lookup"><span data-stu-id="8b74f-381">Set up a host</span></span>

<span data-ttu-id="8b74f-382">主机通常由 `Program` 类中的代码配置、生成和运行。</span><span class="sxs-lookup"><span data-stu-id="8b74f-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="8b74f-383">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="8b74f-383">The `Main` method:</span></span>

* <span data-ttu-id="8b74f-384">调用 `CreateHostBuilder` 方法以创建和配置生成器对象。</span><span class="sxs-lookup"><span data-stu-id="8b74f-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="8b74f-385">对生成器对象调用 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b74f-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="8b74f-386">ASP.NET Core Web 模板会生成以下代码来创建泛型主机：</span><span class="sxs-lookup"><span data-stu-id="8b74f-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="8b74f-387">以下代码会使用非 HTTP 工作负载创建一个泛型主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="8b74f-388">`IHostedService` 实现会添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="8b74f-388">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="8b74f-389">对于 HTTP 工作负荷，`Main` 方法相同，但 `CreateHostBuilder` 调用 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="8b74f-390">上述代码是由 ASP.NET Core 模板生成的。</span><span class="sxs-lookup"><span data-stu-id="8b74f-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="8b74f-391">如果应用使用 Entity Framework Core，不要更改 `CreateHostBuilder` 方法的名称或签名。</span><span class="sxs-lookup"><span data-stu-id="8b74f-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="8b74f-392">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)应查找一个无需运行应用即可配置主机的 `CreateHostBuilder` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b74f-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="8b74f-393">有关详细信息，请参阅[设计时 DbContext 创建](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="8b74f-394">默认生成器设置</span><span class="sxs-lookup"><span data-stu-id="8b74f-394">Default builder settings</span></span>

<span data-ttu-id="8b74f-395"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="8b74f-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="8b74f-396">将[内容根目录](xref:fundamentals/index#content-root)设置为由 <xref:System.IO.Directory.GetCurrentDirectory%2A> 返回的路径。</span><span class="sxs-lookup"><span data-stu-id="8b74f-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="8b74f-397">通过以下项加载主机配置：</span><span class="sxs-lookup"><span data-stu-id="8b74f-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="8b74f-398">前缀为 `DOTNET_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b74f-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="8b74f-399">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="8b74f-399">Command-line arguments.</span></span>
* <span data-ttu-id="8b74f-400">通过以下对象加载应用配置：</span><span class="sxs-lookup"><span data-stu-id="8b74f-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="8b74f-401">appsettings.json。</span><span class="sxs-lookup"><span data-stu-id="8b74f-401">*appsettings.json*.</span></span>
  * <span data-ttu-id="8b74f-402">appsettings.{Environment}.json。</span><span class="sxs-lookup"><span data-stu-id="8b74f-402">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="8b74f-403">[密钥管理器](xref:security/app-secrets) 当应用在 `Development` 环境中运行时。</span><span class="sxs-lookup"><span data-stu-id="8b74f-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="8b74f-404">环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b74f-404">Environment variables.</span></span>
  * <span data-ttu-id="8b74f-405">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="8b74f-405">Command-line arguments.</span></span>
* <span data-ttu-id="8b74f-406">添加以下[日志记录](xref:fundamentals/logging/index)提供程序：</span><span class="sxs-lookup"><span data-stu-id="8b74f-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="8b74f-407">控制台</span><span class="sxs-lookup"><span data-stu-id="8b74f-407">Console</span></span>
  * <span data-ttu-id="8b74f-408">调试</span><span class="sxs-lookup"><span data-stu-id="8b74f-408">Debug</span></span>
  * <span data-ttu-id="8b74f-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="8b74f-409">EventSource</span></span>
  * <span data-ttu-id="8b74f-410">EventLog（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="8b74f-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="8b74f-411">当环境为“开发”时，启用[范围验证](xref:fundamentals/dependency-injection#scope-validation)和[依赖关系验证](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="8b74f-412">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="8b74f-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="8b74f-413">从前缀为 `ASPNETCORE_` 的环境变量加载主机配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="8b74f-414">使用应用的托管配置提供程序将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器设置为 web 服务器并对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="8b74f-415">有关 Kestrel 服务器默认选项，请参阅 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="8b74f-416">添加[主机筛选中间件](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="8b74f-417">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 等于 `true`，则添加[转接头中间件](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="8b74f-418">支持 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="8b74f-418">Enables IIS integration.</span></span> <span data-ttu-id="8b74f-419">有关 IIS 默认选项，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="8b74f-420">本文中后面的[所有应用类型的设置](#settings-for-all-app-types)和[ web 应用的设置](#settings-for-web-apps)部分介绍如何替代默认生成器设置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="8b74f-421">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="8b74f-421">Framework-provided services</span></span>

<span data-ttu-id="8b74f-422">自动注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="8b74f-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="8b74f-423">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8b74f-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="8b74f-424">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="8b74f-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="8b74f-425">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="8b74f-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="8b74f-426">有关框架提供的服务的详细信息，请参阅 <xref:fundamentals/dependency-injection#framework-provided-services>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="8b74f-427">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="8b74f-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="8b74f-428">将 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>（以前称为 `IApplicationLifetime`）服务注入任何类以处理启动后和正常关闭任务。</span><span class="sxs-lookup"><span data-stu-id="8b74f-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="8b74f-429">接口上的三个属性是用于注册应用启动和应用停止事件处理程序方法的取消令牌。</span><span class="sxs-lookup"><span data-stu-id="8b74f-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="8b74f-430">该接口还包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b74f-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="8b74f-431">以下示例是注册 `IHostApplicationLifetime` 事件的 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="8b74f-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="8b74f-432">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="8b74f-432">IHostLifetime</span></span>

<span data-ttu-id="8b74f-433"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 实现控制主机何时启动和何时停止。</span><span class="sxs-lookup"><span data-stu-id="8b74f-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="8b74f-434">使用了已注册的最后一个实现。</span><span class="sxs-lookup"><span data-stu-id="8b74f-434">The last implementation registered is used.</span></span>

<span data-ttu-id="8b74f-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是默认的 `IHostLifetime` 实现。</span><span class="sxs-lookup"><span data-stu-id="8b74f-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="8b74f-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="8b74f-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="8b74f-437">侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="8b74f-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="8b74f-438">解除阻止 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="8b74f-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="8b74f-439">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="8b74f-439">IHostEnvironment</span></span>

<span data-ttu-id="8b74f-440">将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 服务注册到一个类，获取关于以下设置的信息：</span><span class="sxs-lookup"><span data-stu-id="8b74f-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="8b74f-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="8b74f-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="8b74f-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="8b74f-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="8b74f-443">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="8b74f-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="8b74f-444">Web 应用实现 `IWebHostEnvironment` 接口，该接口继承 `IHostEnvironment` 并添加 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="8b74f-445">主机配置</span><span class="sxs-lookup"><span data-stu-id="8b74f-445">Host configuration</span></span>

<span data-ttu-id="8b74f-446">主机配置用于 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 实现的属性。</span><span class="sxs-lookup"><span data-stu-id="8b74f-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="8b74f-447">主机配置可以从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 内的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) 获取。</span><span class="sxs-lookup"><span data-stu-id="8b74f-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="8b74f-448">在 `ConfigureAppConfiguration` 后，`HostBuilderContext.Configuration` 被替换为应用配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="8b74f-449">若要添加主机配置，请对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="8b74f-450">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="8b74f-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="8b74f-451">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="8b74f-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="8b74f-452">`CreateDefaultBuilder` 包含前缀为 `DOTNET_` 的环境变量提供程序和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="8b74f-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b74f-453">对于 web 应用程序，添加前缀为 `ASPNETCORE_` 的环境变量提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b74f-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="8b74f-454">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="8b74f-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="8b74f-455">例如，`ASPNETCORE_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="8b74f-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="8b74f-456">以下示例创建主机配置：</span><span class="sxs-lookup"><span data-stu-id="8b74f-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="8b74f-457">应用配置</span><span class="sxs-lookup"><span data-stu-id="8b74f-457">App configuration</span></span>

<span data-ttu-id="8b74f-458">通过对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="8b74f-459">可多次调用 `ConfigureAppConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="8b74f-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="8b74f-460">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="8b74f-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="8b74f-461">由 `ConfigureAppConfiguration` 创建的配置可以通过 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 获取以用于后续操作，也可以通过 DI 作为服务获取。</span><span class="sxs-lookup"><span data-stu-id="8b74f-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="8b74f-462">主机配置也会添加到应用配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="8b74f-463">有关详细信息，请参阅 [ ASP.NET Core 中的配置](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="8b74f-464">适用于所有应用类型的设置</span><span class="sxs-lookup"><span data-stu-id="8b74f-464">Settings for all app types</span></span>

<span data-ttu-id="8b74f-465">本部分列出了适用于 HTTP 和非 HTTP 工作负荷的主机设置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="8b74f-466">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="8b74f-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="8b74f-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="8b74f-467">ApplicationName</span></span>

<span data-ttu-id="8b74f-468">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="8b74f-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="8b74f-469">键：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="8b74f-469">**Key**: `applicationName`</span></span>  
<span data-ttu-id="8b74f-470">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-470">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-471">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="8b74f-471">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="8b74f-472">**环境变量**：`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="8b74f-472">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="8b74f-473">要设置此值，请使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b74f-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="8b74f-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="8b74f-474">ContentRoot</span></span>

<span data-ttu-id="8b74f-475">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 属性决定主机从什么位置开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="8b74f-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="8b74f-476">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="8b74f-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="8b74f-477">键：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="8b74f-477">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="8b74f-478">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-478">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-479">**默认**：应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8b74f-479">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="8b74f-480">**环境变量**：`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="8b74f-480">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="8b74f-481">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="8b74f-482">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="8b74f-482">For more information, see:</span></span>

* [<span data-ttu-id="8b74f-483">基础知识：内容根目录</span><span class="sxs-lookup"><span data-stu-id="8b74f-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="8b74f-484">WebRoot</span><span class="sxs-lookup"><span data-stu-id="8b74f-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="8b74f-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="8b74f-485">EnvironmentName</span></span>

<span data-ttu-id="8b74f-486">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 属性可以设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="8b74f-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="8b74f-487">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="8b74f-488">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="8b74f-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="8b74f-489">键：`environment`</span><span class="sxs-lookup"><span data-stu-id="8b74f-489">**Key**: `environment`</span></span>  
<span data-ttu-id="8b74f-490">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-490">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-491">**默认**：`Production`</span><span class="sxs-lookup"><span data-stu-id="8b74f-491">**Default**: `Production`</span></span>  
<span data-ttu-id="8b74f-492">**环境变量**：`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="8b74f-492">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="8b74f-493">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="8b74f-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="8b74f-494">ShutdownTimeout</span></span>

<span data-ttu-id="8b74f-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时。</span><span class="sxs-lookup"><span data-stu-id="8b74f-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="8b74f-496">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="8b74f-496">The default value is five seconds.</span></span>  <span data-ttu-id="8b74f-497">在超时时间段中，主机：</span><span class="sxs-lookup"><span data-stu-id="8b74f-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="8b74f-498">触发 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="8b74f-499">尝试停止托管服务，对服务停止失败的错误进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="8b74f-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="8b74f-500">如果在所有托管服务停止之前就达到了超时时间，则会在应用关闭时会终止剩余的所有活动的服务。</span><span class="sxs-lookup"><span data-stu-id="8b74f-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="8b74f-501">即使没有完成处理工作，服务也会停止。</span><span class="sxs-lookup"><span data-stu-id="8b74f-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="8b74f-502">如果停止服务需要额外的时间，请增加超时时间。</span><span class="sxs-lookup"><span data-stu-id="8b74f-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="8b74f-503">键：`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="8b74f-503">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="8b74f-504">类型：`int`</span><span class="sxs-lookup"><span data-stu-id="8b74f-504">**Type**: `int`</span></span>  
<span data-ttu-id="8b74f-505">**默认**：5 秒</span><span class="sxs-lookup"><span data-stu-id="8b74f-505">**Default**: 5 seconds</span></span>  
<span data-ttu-id="8b74f-506">**环境变量**：`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="8b74f-506">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="8b74f-507">若要设置此值，请使用环境变量或配置 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="8b74f-508">以下示例将超时设置为 20 秒：</span><span class="sxs-lookup"><span data-stu-id="8b74f-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="8b74f-509">适用于 Web 应用的设置</span><span class="sxs-lookup"><span data-stu-id="8b74f-509">Settings for web apps</span></span>

<span data-ttu-id="8b74f-510">一些主机设置仅适用于 HTTP 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="8b74f-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="8b74f-511">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="8b74f-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="8b74f-512">`IWebHostBuilder` 上的扩展方法适用于这些设置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="8b74f-513">显示如何调用扩展方法的示例代码假定 `webBuilder` 是 `IWebHostBuilder` 的实例，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="8b74f-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="8b74f-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="8b74f-514">CaptureStartupErrors</span></span>

<span data-ttu-id="8b74f-515">当 `false` 时，启动期间出错导致主机退出。</span><span class="sxs-lookup"><span data-stu-id="8b74f-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="8b74f-516">当 `true` 时，主机在启动期间捕获异常并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="8b74f-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="8b74f-517">键：`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="8b74f-517">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="8b74f-518">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="8b74f-518">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8b74f-519">**默认**：默认为 `false`，除非应用使用 Kestrel 在 IIS 后方运行，其中默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-519">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="8b74f-520">**环境变量**：`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="8b74f-520">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="8b74f-521">若要设置此值，使用配置或调用 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="8b74f-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="8b74f-522">DetailedErrors</span></span>

<span data-ttu-id="8b74f-523">如果启用，或环境为 `Development`，应用会捕获详细错误。</span><span class="sxs-lookup"><span data-stu-id="8b74f-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="8b74f-524">键：`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="8b74f-524">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="8b74f-525">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="8b74f-525">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8b74f-526">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="8b74f-526">**Default**: `false`</span></span>  
<span data-ttu-id="8b74f-527">**环境变量**：`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="8b74f-527">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="8b74f-528">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="8b74f-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="8b74f-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="8b74f-530">承载启动程序集的以分号分隔的字符串在启动时加载。</span><span class="sxs-lookup"><span data-stu-id="8b74f-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="8b74f-531">虽然配置值默认为空字符串，但是承载启动程序集会始终包含应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="8b74f-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="8b74f-532">提供承载启动程序集时，当应用在启动过程中生成其公用服务时将它们添加到应用的程序集加载。</span><span class="sxs-lookup"><span data-stu-id="8b74f-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="8b74f-533">键：`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="8b74f-533">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="8b74f-534">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-534">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-535">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="8b74f-535">**Default**: Empty string</span></span>  
<span data-ttu-id="8b74f-536">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8b74f-536">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="8b74f-537">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="8b74f-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="8b74f-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="8b74f-539">承载启动程序集的以分号分隔的字符串在启动时排除。</span><span class="sxs-lookup"><span data-stu-id="8b74f-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="8b74f-540">键：`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="8b74f-540">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="8b74f-541">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-541">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-542">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="8b74f-542">**Default**: Empty string</span></span>  
<span data-ttu-id="8b74f-543">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8b74f-543">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="8b74f-544">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="8b74f-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="8b74f-545">HTTPS_Port</span></span>

<span data-ttu-id="8b74f-546">HTTPS 重定向端口。</span><span class="sxs-lookup"><span data-stu-id="8b74f-546">The HTTPS redirect port.</span></span> <span data-ttu-id="8b74f-547">用于[强制实施 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="8b74f-548">键：`https_port`</span><span class="sxs-lookup"><span data-stu-id="8b74f-548">**Key**: `https_port`</span></span>  
<span data-ttu-id="8b74f-549">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-549">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-550">**默认**：未设置默认值。</span><span class="sxs-lookup"><span data-stu-id="8b74f-550">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="8b74f-551">**环境变量**：`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="8b74f-551">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="8b74f-552">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="8b74f-553">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="8b74f-553">PreferHostingUrls</span></span>

<span data-ttu-id="8b74f-554">指示主机是否应该侦听使用 `IWebHostBuilder` 配置的 URL，而不是使用 `IServer` 实现配置的 URL。</span><span class="sxs-lookup"><span data-stu-id="8b74f-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="8b74f-555">键：`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="8b74f-555">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="8b74f-556">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="8b74f-556">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8b74f-557">**默认**：`true`</span><span class="sxs-lookup"><span data-stu-id="8b74f-557">**Default**: `true`</span></span>  
<span data-ttu-id="8b74f-558">**环境变量**：`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="8b74f-558">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="8b74f-559">若要设置此值，请使用环境变量或调用 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="8b74f-560">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="8b74f-560">PreventHostingStartup</span></span>

<span data-ttu-id="8b74f-561">阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="8b74f-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="8b74f-562">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="8b74f-563">键：`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="8b74f-563">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="8b74f-564">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="8b74f-564">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="8b74f-565">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="8b74f-565">**Default**: `false`</span></span>  
<span data-ttu-id="8b74f-566">**环境变量**：`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="8b74f-566">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="8b74f-567">若要设置此值，请使用环境变量或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="8b74f-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="8b74f-568">StartupAssembly</span></span>

<span data-ttu-id="8b74f-569">要搜索 `Startup` 类的程序集。</span><span class="sxs-lookup"><span data-stu-id="8b74f-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="8b74f-570">键：`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="8b74f-570">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="8b74f-571">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-571">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-572">**默认**：应用的程序集</span><span class="sxs-lookup"><span data-stu-id="8b74f-572">**Default**: The app's assembly</span></span>  
<span data-ttu-id="8b74f-573">**环境变量**：`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="8b74f-573">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="8b74f-574">若要设置此值，请使用环境变量或调用 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="8b74f-575">`UseStartup` 可以采用程序集名称 (`string`) 或类型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="8b74f-576">如果调用多个 `UseStartup` 方法，优先选择最后一个方法。</span><span class="sxs-lookup"><span data-stu-id="8b74f-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="8b74f-577">URL</span><span class="sxs-lookup"><span data-stu-id="8b74f-577">URLs</span></span>

<span data-ttu-id="8b74f-578">IP 地址或主机地址的分号分隔列表，其中包含服务器应针对请求侦听的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="8b74f-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="8b74f-579">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="8b74f-580">使用“\*”指示服务器应针对请求侦听的使用特定端口和协议（例如 `http://*:5000`）的 IP 地址或主机名。</span><span class="sxs-lookup"><span data-stu-id="8b74f-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="8b74f-581">协议（`http://` 或 `https://`）必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="8b74f-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="8b74f-582">不同的服务器支持的格式有所不同。</span><span class="sxs-lookup"><span data-stu-id="8b74f-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="8b74f-583">键：`urls`</span><span class="sxs-lookup"><span data-stu-id="8b74f-583">**Key**: `urls`</span></span>  
<span data-ttu-id="8b74f-584">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-584">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-585">**默认值**：`http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="8b74f-585">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="8b74f-586">**环境变量**：`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="8b74f-586">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="8b74f-587">若要设置此值，请使用环境变量或调用 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="8b74f-588">Kestrel 具有自己的终结点配置 API。</span><span class="sxs-lookup"><span data-stu-id="8b74f-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="8b74f-589">有关详细信息，请参阅 <xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="8b74f-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="8b74f-590">WebRoot</span></span>

<span data-ttu-id="8b74f-591">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) 属性可确定应用静态资产的相对路径。</span><span class="sxs-lookup"><span data-stu-id="8b74f-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="8b74f-592">如果该路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b74f-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="8b74f-593">键：`webroot`</span><span class="sxs-lookup"><span data-stu-id="8b74f-593">**Key**: `webroot`</span></span>  
<span data-ttu-id="8b74f-594">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-594">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-595">**默认**：默认值为 `wwwroot`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-595">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="8b74f-596">{content root}/wwwroot 的路径必须存在。</span><span class="sxs-lookup"><span data-stu-id="8b74f-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="8b74f-597">**环境变量**：`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="8b74f-597">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="8b74f-598">若要设置此值，请使用环境变量或对 `IWebHostBuilder` 调用 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="8b74f-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="8b74f-599">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="8b74f-599">For more information, see:</span></span>

* [<span data-ttu-id="8b74f-600">基础知识：Web 根目录</span><span class="sxs-lookup"><span data-stu-id="8b74f-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="8b74f-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="8b74f-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="8b74f-602">管理主机生存期</span><span class="sxs-lookup"><span data-stu-id="8b74f-602">Manage the host lifetime</span></span>

<span data-ttu-id="8b74f-603">对生成的 <xref:Microsoft.Extensions.Hosting.IHost> 实现调用方法，以启动和停止应用。</span><span class="sxs-lookup"><span data-stu-id="8b74f-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="8b74f-604">这些方法会影响所有在服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="8b74f-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="8b74f-605">运行</span><span class="sxs-lookup"><span data-stu-id="8b74f-605">Run</span></span>

<span data-ttu-id="8b74f-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="8b74f-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-607">RunAsync</span></span>

<span data-ttu-id="8b74f-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="8b74f-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-609">RunConsoleAsync</span></span>

<span data-ttu-id="8b74f-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="8b74f-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="8b74f-611">Start</span><span class="sxs-lookup"><span data-stu-id="8b74f-611">Start</span></span>

<span data-ttu-id="8b74f-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="8b74f-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-613">StartAsync</span></span>

<span data-ttu-id="8b74f-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动主机并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="8b74f-615">在 `StartAsync` 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="8b74f-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="8b74f-616">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="8b74f-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="8b74f-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-617">StopAsync</span></span>

<span data-ttu-id="8b74f-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="8b74f-619">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="8b74f-619">WaitForShutdown</span></span>

<span data-ttu-id="8b74f-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 阻止调用线程，直到 IHostLifetime 触发关闭，例如通过 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM。</span><span class="sxs-lookup"><span data-stu-id="8b74f-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="8b74f-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="8b74f-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="8b74f-623">外部控件</span><span class="sxs-lookup"><span data-stu-id="8b74f-623">External control</span></span>

<span data-ttu-id="8b74f-624">使用可从外部调用的方法，能够实现对主机生存期的直接控制：</span><span class="sxs-lookup"><span data-stu-id="8b74f-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b74f-625">ASP.NET Core 应用配置和启动主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="8b74f-626">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="8b74f-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="8b74f-627">本文介绍 ASP.NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)，该主机用于无法处理 HTTP 请求的应用。</span><span class="sxs-lookup"><span data-stu-id="8b74f-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="8b74f-628">泛型主机的用途是将 HTTP 管道从 Web 主机 API 中分离出来，从而启用更多的主机方案。</span><span class="sxs-lookup"><span data-stu-id="8b74f-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="8b74f-629">基于泛型主机的消息、后台任务和其他非 HTTP 工作负载可从横切功能（如配置、依赖关系注入 [DI] 和日志记录）中受益。</span><span class="sxs-lookup"><span data-stu-id="8b74f-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="8b74f-630">泛型主机是 ASP.NET Core 2.1 中的新增功能，不适用于 Web 承载方案。</span><span class="sxs-lookup"><span data-stu-id="8b74f-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="8b74f-631">对于 Web 承载方案，请使用 [Web 主机](xref:fundamentals/host/web-host)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8b74f-632">泛型主机将在未来版本中替换 Web 主机，并在 HTTP 和非 HTTP 方案中充当主要的主机 API。</span><span class="sxs-lookup"><span data-stu-id="8b74f-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="8b74f-633">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8b74f-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8b74f-634">在 [Visual Studio Code](https://code.visualstudio.com/) 中运行示例应用时，请使用外部或集成终端。</span><span class="sxs-lookup"><span data-stu-id="8b74f-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="8b74f-635">请勿在 `internalConsole` 中运行示例。</span><span class="sxs-lookup"><span data-stu-id="8b74f-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="8b74f-636">在 Visual Studio Code 中设置控制台：</span><span class="sxs-lookup"><span data-stu-id="8b74f-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="8b74f-637">打开 .vscode/launch.json 文件。</span><span class="sxs-lookup"><span data-stu-id="8b74f-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="8b74f-638">在 .NET Core 启动（控制台）配置中，找到控制台条目 。</span><span class="sxs-lookup"><span data-stu-id="8b74f-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="8b74f-639">将值设置为 `externalTerminal` 或 `integratedTerminal`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="8b74f-640">介绍</span><span class="sxs-lookup"><span data-stu-id="8b74f-640">Introduction</span></span>

<span data-ttu-id="8b74f-641">通用主机库位于 <xref:Microsoft.Extensions.Hosting> 命名空间中，由 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="8b74f-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="8b74f-642">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)（ASP.NET Core 2.1 或更高版本）中包括 `Microsoft.Extensions.Hosting` 包。</span><span class="sxs-lookup"><span data-stu-id="8b74f-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="8b74f-643"><xref:Microsoft.Extensions.Hosting.IHostedService> 是执行代码的入口点。</span><span class="sxs-lookup"><span data-stu-id="8b74f-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="8b74f-644">每个 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现都按照 [ConfigureServices 中服务注册](#configureservices)的顺序执行。</span><span class="sxs-lookup"><span data-stu-id="8b74f-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="8b74f-645">主机启动时，每个 <xref:Microsoft.Extensions.Hosting.IHostedService> 上都会调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>，主机正常关闭时，以反向注册顺序调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="8b74f-646">设置主机</span><span class="sxs-lookup"><span data-stu-id="8b74f-646">Set up a host</span></span>

<span data-ttu-id="8b74f-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> 是供库和应用初始化、生成和运行主机的主要组件：</span><span class="sxs-lookup"><span data-stu-id="8b74f-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="8b74f-648">选项</span><span class="sxs-lookup"><span data-stu-id="8b74f-648">Options</span></span>

<span data-ttu-id="8b74f-649"><xref:Microsoft.Extensions.Hosting.HostOptions> 配置 <xref:Microsoft.Extensions.Hosting.IHost> 的选项。</span><span class="sxs-lookup"><span data-stu-id="8b74f-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="8b74f-650">关闭超时值</span><span class="sxs-lookup"><span data-stu-id="8b74f-650">Shutdown timeout</span></span>

<span data-ttu-id="8b74f-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时值。</span><span class="sxs-lookup"><span data-stu-id="8b74f-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="8b74f-652">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="8b74f-652">The default value is five seconds.</span></span>

<span data-ttu-id="8b74f-653">`Program.Main` 中的以下选项配置将默认值为 5 秒的关闭超时值增加至 20 秒：</span><span class="sxs-lookup"><span data-stu-id="8b74f-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="8b74f-654">默认服务</span><span class="sxs-lookup"><span data-stu-id="8b74f-654">Default services</span></span>

<span data-ttu-id="8b74f-655">在主机初始化期间注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="8b74f-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="8b74f-656">[环境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="8b74f-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="8b74f-657">[配置](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="8b74f-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="8b74f-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="8b74f-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="8b74f-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="8b74f-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="8b74f-660">[选项](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="8b74f-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="8b74f-661">[日志记录](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="8b74f-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="8b74f-662">主机配置</span><span class="sxs-lookup"><span data-stu-id="8b74f-662">Host configuration</span></span>

<span data-ttu-id="8b74f-663">主机配置的创建方式如下：</span><span class="sxs-lookup"><span data-stu-id="8b74f-663">Host configuration is created by:</span></span>

* <span data-ttu-id="8b74f-664">调用 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的扩展方法以设置[“内容根”](#content-root)和[“环境”](#environment)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="8b74f-665">从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中的配置提供程序读取配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="8b74f-666">扩展方法</span><span class="sxs-lookup"><span data-stu-id="8b74f-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="8b74f-667">应用程序键（名称）</span><span class="sxs-lookup"><span data-stu-id="8b74f-667">Application key (name)</span></span>

<span data-ttu-id="8b74f-668">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="8b74f-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="8b74f-669">要显式设置值，请使用 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)：</span><span class="sxs-lookup"><span data-stu-id="8b74f-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="8b74f-670">键：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="8b74f-670">**Key**: `applicationName`</span></span>  
<span data-ttu-id="8b74f-671">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-671">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-672">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="8b74f-672">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="8b74f-673">**设置使用**：`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="8b74f-673">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="8b74f-674">**环境变量**：`<PREFIX_>APPLICATIONNAME`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="8b74f-674">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="8b74f-675">内容根</span><span class="sxs-lookup"><span data-stu-id="8b74f-675">Content root</span></span>

<span data-ttu-id="8b74f-676">此设置确定主机从哪里开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="8b74f-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="8b74f-677">键：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="8b74f-677">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="8b74f-678">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-678">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-679">**默认**：默认为应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8b74f-679">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="8b74f-680">**设置使用**：`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="8b74f-680">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="8b74f-681">**环境变量**：`<PREFIX_>CONTENTROOT`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="8b74f-681">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="8b74f-682">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="8b74f-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="8b74f-683">有关详细信息，请参阅[基础知识：内容根目录](xref:fundamentals/index#content-root)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="8b74f-684">环境</span><span class="sxs-lookup"><span data-stu-id="8b74f-684">Environment</span></span>

<span data-ttu-id="8b74f-685">设置应用的[环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="8b74f-686">键：`environment`</span><span class="sxs-lookup"><span data-stu-id="8b74f-686">**Key**: `environment`</span></span>  
<span data-ttu-id="8b74f-687">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="8b74f-687">**Type**: `string`</span></span>  
<span data-ttu-id="8b74f-688">**默认**：`Production`</span><span class="sxs-lookup"><span data-stu-id="8b74f-688">**Default**: `Production`</span></span>  
<span data-ttu-id="8b74f-689">**设置使用**：`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="8b74f-689">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="8b74f-690">**环境变量**：`<PREFIX_>ENVIRONMENT`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="8b74f-690">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="8b74f-691">可将环境设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="8b74f-691">The environment can be set to any value.</span></span> <span data-ttu-id="8b74f-692">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="8b74f-693">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="8b74f-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="8b74f-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="8b74f-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="8b74f-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 来为主机创建 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="8b74f-696">主机配置用于初始化 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>，以供在应用的构建过程中使用。</span><span class="sxs-lookup"><span data-stu-id="8b74f-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="8b74f-697">可多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="8b74f-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="8b74f-698">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="8b74f-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="8b74f-699">默认情况下不包括提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b74f-699">No providers are included by default.</span></span> <span data-ttu-id="8b74f-700">必须在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中显式指定应用所需的任何配置提供程序，包括：</span><span class="sxs-lookup"><span data-stu-id="8b74f-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="8b74f-701">文件配置（例如，来自 hostsettings.json 文件）。</span><span class="sxs-lookup"><span data-stu-id="8b74f-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="8b74f-702">环境变量配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-702">Environment variable configuration.</span></span>
* <span data-ttu-id="8b74f-703">命令行参数配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="8b74f-704">任何其他所需的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8b74f-704">Any other required configuration providers.</span></span>

<span data-ttu-id="8b74f-705">通过使用 `SetBasePath` 指定应用的基本路径，然后调用其中一个[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)，可以启用主机的文件配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="8b74f-706">示例应用使用 JSON 文件 hostsettings.json，并调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 来使用文件的主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-706">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="8b74f-707">要添加主机的[环境变量配置](xref:fundamentals/configuration/index#environment-variables-configuration-provider)，请在主机生成器上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="8b74f-708">`AddEnvironmentVariables` 接受用户定义的前缀（可选）。</span><span class="sxs-lookup"><span data-stu-id="8b74f-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="8b74f-709">示例应用使用前缀 `PREFIX_`。</span><span class="sxs-lookup"><span data-stu-id="8b74f-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="8b74f-710">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="8b74f-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="8b74f-711">配置示例应用的主机后，`PREFIX_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="8b74f-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="8b74f-712">在开发过程中，如果使用 [Visual Studio](https://visualstudio.microsoft.com) 或通过 `dotnet run` 运行应用，可能会在 Properties/launchSettings.json 文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b74f-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="8b74f-713">若在开发过程中使用 [Visual Studio Code](https://code.visualstudio.com/)，可能会在 .vscode/launch.json 文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="8b74f-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="8b74f-714">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8b74f-715">通过调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 可添加[命令行配置](xref:fundamentals/configuration/index#command-line-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="8b74f-716">最后添加命令行配置以允许命令行参数替代之前配置提供程序提供的配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="8b74f-717">hostsettings.json：</span><span class="sxs-lookup"><span data-stu-id="8b74f-717">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="8b74f-718">可以通过 [applicationName](#application-key-name) 和 [contentRoot](#content-root) 键提供其他配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="8b74f-719">示例 `HostBuilder` 配置使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="8b74f-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="8b74f-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8b74f-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8b74f-721">通过在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 实现上调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="8b74f-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 来为应用创建 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="8b74f-723">可多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="8b74f-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="8b74f-724">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="8b74f-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="8b74f-725">[HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 中提供 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建的配置，以供进行后续操作和在 <xref:Microsoft.Extensions.Hosting.IHost.Services*> 中使用。</span><span class="sxs-lookup"><span data-stu-id="8b74f-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="8b74f-726">应用配置会自动接收 [ConfigureHostConfiguration](#configurehostconfiguration) 提供的主机配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="8b74f-727">示例应用配置使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="8b74f-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="8b74f-728">appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="8b74f-728">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="8b74f-729">appsettings.Development.json：</span><span class="sxs-lookup"><span data-stu-id="8b74f-729">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="8b74f-730">appsettings.Production.json：</span><span class="sxs-lookup"><span data-stu-id="8b74f-730">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="8b74f-731">要将设置文件移动到输出目录，请在项目文件中将设置文件指定为 [MSBuild 项目项](/visualstudio/msbuild/common-msbuild-project-items)。</span><span class="sxs-lookup"><span data-stu-id="8b74f-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="8b74f-732">示例应用移动具有以下 `<Content>` 项的 JSON 应用设置文件和 hostsettings.json：</span><span class="sxs-lookup"><span data-stu-id="8b74f-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="8b74f-733">配置扩展方法（如 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 和 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>）需要其他 NuGet 包（如 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) 和 [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)）。</span><span class="sxs-lookup"><span data-stu-id="8b74f-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="8b74f-734">除非应用使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，否则除了核心 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) 包之外，还必须将这些包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="8b74f-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="8b74f-735">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="8b74f-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="8b74f-736">ConfigureServices</span></span>

<span data-ttu-id="8b74f-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 将服务添加到应用的[依赖关系](xref:fundamentals/dependency-injection)注入容器。</span><span class="sxs-lookup"><span data-stu-id="8b74f-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8b74f-738">可多次调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="8b74f-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="8b74f-739">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="8b74f-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="8b74f-740">有关详细信息，请参阅 <xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="8b74f-741">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)使用 `AddHostedService` 扩展方法向应用添加生存期事件 `LifetimeEventsHostedService` 和定时后台任务 `TimedHostedService` 服务：</span><span class="sxs-lookup"><span data-stu-id="8b74f-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="8b74f-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="8b74f-742">ConfigureLogging</span></span>

<span data-ttu-id="8b74f-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 添加了一个委托来配置提供的 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="8b74f-744">可以利用相加结果多次调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="8b74f-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="8b74f-745">UseConsoleLifetime</span></span>

<span data-ttu-id="8b74f-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="8b74f-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="8b74f-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 解除阻止 [ RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="8b74f-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="8b74f-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 预注册为默认生存期实现。</span><span class="sxs-lookup"><span data-stu-id="8b74f-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="8b74f-749">使用注册的最后一个生存期。</span><span class="sxs-lookup"><span data-stu-id="8b74f-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="8b74f-750">容器配置</span><span class="sxs-lookup"><span data-stu-id="8b74f-750">Container configuration</span></span>

<span data-ttu-id="8b74f-751">为支持插入其他容器中，主机可以接受 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="8b74f-752">提供工厂不属于 DI 容器注册，而是用于创建具体 DI 容器的主机内部函数。</span><span class="sxs-lookup"><span data-stu-id="8b74f-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="8b74f-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) 重写用于创建应用的服务提供程序的默认工厂。</span><span class="sxs-lookup"><span data-stu-id="8b74f-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="8b74f-754"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 方法托管自定义容器配置。</span><span class="sxs-lookup"><span data-stu-id="8b74f-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="8b74f-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 提供在基础主机 API 的基础之上配置容器的强类型体验。</span><span class="sxs-lookup"><span data-stu-id="8b74f-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="8b74f-756">可以利用相加结果多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="8b74f-757">为应用创建服务容器：</span><span class="sxs-lookup"><span data-stu-id="8b74f-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="8b74f-758">提供服务容器工厂：</span><span class="sxs-lookup"><span data-stu-id="8b74f-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="8b74f-759">使用该工厂并为应用配置自定义服务容器：</span><span class="sxs-lookup"><span data-stu-id="8b74f-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="8b74f-760">扩展性</span><span class="sxs-lookup"><span data-stu-id="8b74f-760">Extensibility</span></span>

<span data-ttu-id="8b74f-761">在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上使用扩展方法实现主机扩展性。</span><span class="sxs-lookup"><span data-stu-id="8b74f-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="8b74f-762">以下示例介绍扩展方法如何使用 <xref:fundamentals/host/hosted-services> 中所示的 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 示例来扩展 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 实现。</span><span class="sxs-lookup"><span data-stu-id="8b74f-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="8b74f-763">应用建立 `UseHostedService` 扩展方法，以注册在 `T` 中传递的托管服务：</span><span class="sxs-lookup"><span data-stu-id="8b74f-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="8b74f-764">管理主机</span><span class="sxs-lookup"><span data-stu-id="8b74f-764">Manage the host</span></span>

<span data-ttu-id="8b74f-765"><xref:Microsoft.Extensions.Hosting.IHost> 实现负责启动和停止服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="8b74f-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="8b74f-766">运行</span><span class="sxs-lookup"><span data-stu-id="8b74f-766">Run</span></span>

<span data-ttu-id="8b74f-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机：</span><span class="sxs-lookup"><span data-stu-id="8b74f-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="8b74f-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-768">RunAsync</span></span>

<span data-ttu-id="8b74f-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="8b74f-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="8b74f-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-770">RunConsoleAsync</span></span>

<span data-ttu-id="8b74f-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="8b74f-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="8b74f-772">Start 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-772">Start and StopAsync</span></span>

<span data-ttu-id="8b74f-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="8b74f-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="8b74f-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="8b74f-775">StartAsync 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="8b74f-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动应用。</span><span class="sxs-lookup"><span data-stu-id="8b74f-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="8b74f-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 停止应用。</span><span class="sxs-lookup"><span data-stu-id="8b74f-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="8b74f-778">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="8b74f-778">WaitForShutdown</span></span>

<span data-ttu-id="8b74f-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 通过 <xref:Microsoft.Extensions.Hosting.IHostLifetime> 触发，例如 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`（侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="8b74f-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="8b74f-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="8b74f-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="8b74f-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="8b74f-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="8b74f-783">外部控件</span><span class="sxs-lookup"><span data-stu-id="8b74f-783">External control</span></span>

<span data-ttu-id="8b74f-784">使用可从外部调用的方法，能够实现主机的外部控件：</span><span class="sxs-lookup"><span data-stu-id="8b74f-784">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="8b74f-785">在 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="8b74f-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="8b74f-786">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="8b74f-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="8b74f-787">IHostingEnvironment 接口</span><span class="sxs-lookup"><span data-stu-id="8b74f-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="8b74f-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 提供有关应用托管环境的信息。</span><span class="sxs-lookup"><span data-stu-id="8b74f-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="8b74f-789">使用[构造函数注入](xref:fundamentals/dependency-injection)获取 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 以使用其属性和扩展方法：</span><span class="sxs-lookup"><span data-stu-id="8b74f-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="8b74f-790">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="8b74f-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="8b74f-791">IApplicationLifetime 接口</span><span class="sxs-lookup"><span data-stu-id="8b74f-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="8b74f-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 允许启动后和关闭活动，包括正常关闭请求。</span><span class="sxs-lookup"><span data-stu-id="8b74f-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="8b74f-793">接口上的三个属性是用于注册 <xref:System.Action> 方法（用于定义启动和关闭事件）的取消标记。</span><span class="sxs-lookup"><span data-stu-id="8b74f-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="8b74f-794">取消标记</span><span class="sxs-lookup"><span data-stu-id="8b74f-794">Cancellation Token</span></span> | <span data-ttu-id="8b74f-795">触发条件</span><span class="sxs-lookup"><span data-stu-id="8b74f-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="8b74f-796">主机已完全启动。</span><span class="sxs-lookup"><span data-stu-id="8b74f-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="8b74f-797">主机正在完成正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8b74f-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="8b74f-798">应处理所有请求。</span><span class="sxs-lookup"><span data-stu-id="8b74f-798">All requests should be processed.</span></span> <span data-ttu-id="8b74f-799">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="8b74f-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="8b74f-800">主机正在执行正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8b74f-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="8b74f-801">仍在处理请求。</span><span class="sxs-lookup"><span data-stu-id="8b74f-801">Requests may still be processing.</span></span> <span data-ttu-id="8b74f-802">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="8b74f-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="8b74f-803">构造函数将 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 服务注入到任何类中。</span><span class="sxs-lookup"><span data-stu-id="8b74f-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="8b74f-804">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)将构造函数注入到 `LifetimeEventsHostedService` 类（一个 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现）中，用于注册事件。</span><span class="sxs-lookup"><span data-stu-id="8b74f-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="8b74f-805">LifetimeEventsHostedService.cs：</span><span class="sxs-lookup"><span data-stu-id="8b74f-805">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="8b74f-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 请求终止应用。</span><span class="sxs-lookup"><span data-stu-id="8b74f-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="8b74f-807">以下类在调用类的 `Shutdown` 方法时使用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 正常关闭应用：</span><span class="sxs-lookup"><span data-stu-id="8b74f-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8b74f-808">其他资源</span><span class="sxs-lookup"><span data-stu-id="8b74f-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
