---
title: .NET 通用主机
author: tdykstra
description: 了解 .NET Core 泛型主机，该主机负责应用启动和生存期管理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 1582955cd18e6739111af05c9a892cd5cb4e270d
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007241"
---
# <a name="net-generic-host"></a><span data-ttu-id="277d1-103">.NET 通用主机</span><span class="sxs-lookup"><span data-stu-id="277d1-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="277d1-104">本文介绍了 .NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 并提供有关使用方法的指南。</span><span class="sxs-lookup"><span data-stu-id="277d1-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="277d1-105">什么是主机？</span><span class="sxs-lookup"><span data-stu-id="277d1-105">What's a host?</span></span>

<span data-ttu-id="277d1-106">主机是封装应用资源的对象，例如  ：</span><span class="sxs-lookup"><span data-stu-id="277d1-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="277d1-107">依赖关系注入 (DI)</span><span class="sxs-lookup"><span data-stu-id="277d1-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="277d1-108">Logging</span><span class="sxs-lookup"><span data-stu-id="277d1-108">Logging</span></span>
* <span data-ttu-id="277d1-109">配置</span><span class="sxs-lookup"><span data-stu-id="277d1-109">Configuration</span></span>
* <span data-ttu-id="277d1-110">`IHostedService` 实现</span><span class="sxs-lookup"><span data-stu-id="277d1-110">`IHostedService` implementations</span></span>

<span data-ttu-id="277d1-111">启动主机时，它对它在 DI 容器中找到的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的每个实现调用 `IHostedService.StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="277d1-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="277d1-112">在 web 应用中，其中一个 `IHostedService` 实现是启动 [HTTP 服务器实现](xref:fundamentals/index#servers)的 web 服务。</span><span class="sxs-lookup"><span data-stu-id="277d1-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="277d1-113">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="277d1-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="277d1-114">在低于 3.0 的 ASP.NET Core 版本中，[Web 主机](xref:fundamentals/host/web-host)用于 HTTP 工作负载。</span><span class="sxs-lookup"><span data-stu-id="277d1-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="277d1-115">不再建议将 Web 主机用于 Web 应用，但该主机仍可仅用于后向兼容性。</span><span class="sxs-lookup"><span data-stu-id="277d1-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="277d1-116">设置主机</span><span class="sxs-lookup"><span data-stu-id="277d1-116">Set up a host</span></span>

<span data-ttu-id="277d1-117">主机通常由 `Program` 类中的代码配置、生成和运行。</span><span class="sxs-lookup"><span data-stu-id="277d1-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="277d1-118">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="277d1-118">The `Main` method:</span></span>

* <span data-ttu-id="277d1-119">调用 `CreateHostBuilder` 方法以创建和配置生成器对象。</span><span class="sxs-lookup"><span data-stu-id="277d1-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="277d1-120">对生成器对象调用 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="277d1-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="277d1-121">以下是用于非 HTTP 工作负载的 Program.cs  代码，其中单个 `IHostedService` 实现添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="277d1-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="277d1-122">对于 HTTP 工作负荷，`Main` 方法相同，但 `CreateHostBuilder` 调用 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="277d1-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="277d1-123">如果应用使用 Entity Framework Core，不要更改 `CreateHostBuilder` 方法的名称或签名。</span><span class="sxs-lookup"><span data-stu-id="277d1-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="277d1-124">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)应查找一个无需运行应用即可配置主机的 `CreateHostBuilder` 方法。</span><span class="sxs-lookup"><span data-stu-id="277d1-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="277d1-125">有关详细信息，请参阅[设计时 DbContext 创建](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="277d1-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="277d1-126">默认生成器设置</span><span class="sxs-lookup"><span data-stu-id="277d1-126">Default builder settings</span></span> 

<span data-ttu-id="277d1-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 方法：</span><span class="sxs-lookup"><span data-stu-id="277d1-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="277d1-128">将[内容根目录](xref:fundamentals/index#content-root)设置为由 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的路径。</span><span class="sxs-lookup"><span data-stu-id="277d1-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="277d1-129">通过以下项加载主机配置：</span><span class="sxs-lookup"><span data-stu-id="277d1-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="277d1-130">前缀为“DOTNET_”的环境变量。</span><span class="sxs-lookup"><span data-stu-id="277d1-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="277d1-131">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="277d1-131">Command-line arguments.</span></span>
* <span data-ttu-id="277d1-132">通过以下对象加载应用配置：</span><span class="sxs-lookup"><span data-stu-id="277d1-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="277d1-133">appsettings.json  。</span><span class="sxs-lookup"><span data-stu-id="277d1-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="277d1-134">appsettings.{Environment}.json  。</span><span class="sxs-lookup"><span data-stu-id="277d1-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="277d1-135">[密钥管理器](xref:security/app-secrets) 当应用在 `Development` 环境中运行时。</span><span class="sxs-lookup"><span data-stu-id="277d1-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="277d1-136">环境变量。</span><span class="sxs-lookup"><span data-stu-id="277d1-136">Environment variables.</span></span>
  * <span data-ttu-id="277d1-137">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="277d1-137">Command-line arguments.</span></span>
* <span data-ttu-id="277d1-138">添加以下[日志记录](xref:fundamentals/logging/index)提供程序：</span><span class="sxs-lookup"><span data-stu-id="277d1-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="277d1-139">控制台</span><span class="sxs-lookup"><span data-stu-id="277d1-139">Console</span></span>
  * <span data-ttu-id="277d1-140">调试</span><span class="sxs-lookup"><span data-stu-id="277d1-140">Debug</span></span>
  * <span data-ttu-id="277d1-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="277d1-141">EventSource</span></span>
  * <span data-ttu-id="277d1-142">EventLog（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="277d1-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="277d1-143">当环境为“开发”时，启用[范围验证](xref:fundamentals/dependency-injection#scope-validation)和[依赖关系验证](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="277d1-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="277d1-144">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="277d1-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="277d1-145">从前缀为“ASPNETCORE_”的环境变量加载主机配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="277d1-146">使用应用的托管配置提供程序将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器设置为 web 服务器并对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="277d1-147">有关 Kestrel 服务器默认选项，请参阅 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="277d1-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="277d1-148">添加[主机筛选中间件](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="277d1-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="277d1-149">如果 ASPNETCORE_FORWARDEDHEADERS_ENABLED=true，请添加[转发标头中间件](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)。</span><span class="sxs-lookup"><span data-stu-id="277d1-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="277d1-150">支持 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="277d1-150">Enables IIS integration.</span></span> <span data-ttu-id="277d1-151">有关 IIS 默认选项，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="277d1-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="277d1-152">本文中后面的[所有应用类型的设置](#settings-for-all-app-types)和[ web 应用的设置](#settings-for-web-apps)部分介绍如何替代默认生成器设置。</span><span class="sxs-lookup"><span data-stu-id="277d1-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="277d1-153">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="277d1-153">Framework-provided services</span></span>

<span data-ttu-id="277d1-154">自动注册的服务包括：</span><span class="sxs-lookup"><span data-stu-id="277d1-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="277d1-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="277d1-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="277d1-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="277d1-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="277d1-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="277d1-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="277d1-158">有关框架提供的服务的详细信息，请参阅 <xref:fundamentals/dependency-injection#framework-provided-services>。</span><span class="sxs-lookup"><span data-stu-id="277d1-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="277d1-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="277d1-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="277d1-160">将 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>（以前称为 `IApplicationLifetime`）服务注入任何类以处理启动后和正常关闭任务。</span><span class="sxs-lookup"><span data-stu-id="277d1-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="277d1-161">接口上的三个属性是用于注册应用启动和应用停止事件处理程序方法的取消令牌。</span><span class="sxs-lookup"><span data-stu-id="277d1-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="277d1-162">该接口还包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="277d1-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="277d1-163">以下示例是注册 `IHostApplicationLifetime` 事件的 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="277d1-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="277d1-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="277d1-164">IHostLifetime</span></span>

<span data-ttu-id="277d1-165"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 实现控制主机何时启动和何时停止。</span><span class="sxs-lookup"><span data-stu-id="277d1-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="277d1-166">使用了已注册的最后一个实现。</span><span class="sxs-lookup"><span data-stu-id="277d1-166">The last implementation registered is used.</span></span>

<span data-ttu-id="277d1-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> 是默认的 `IHostLifetime` 实现。</span><span class="sxs-lookup"><span data-stu-id="277d1-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="277d1-168">`ConsoleLifetime`：</span><span class="sxs-lookup"><span data-stu-id="277d1-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="277d1-169">侦听 Ctrl+C/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="277d1-169">listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="277d1-170">解除阻止 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="277d1-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="277d1-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="277d1-171">IHostEnvironment</span></span>

<span data-ttu-id="277d1-172">将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 服务注册到一个类，获取关于以下项的信息：</span><span class="sxs-lookup"><span data-stu-id="277d1-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="277d1-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="277d1-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="277d1-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="277d1-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="277d1-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="277d1-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="277d1-176">Web 应用实现 `IWebHostEnvironment` 接口，该接口继承 `IHostEnvironment` 并添加：</span><span class="sxs-lookup"><span data-stu-id="277d1-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds:</span></span>

* [<span data-ttu-id="277d1-177">WebRootPath</span><span class="sxs-lookup"><span data-stu-id="277d1-177">WebRootPath</span></span>](#webroot)

## <a name="host-configuration"></a><span data-ttu-id="277d1-178">主机配置</span><span class="sxs-lookup"><span data-stu-id="277d1-178">Host configuration</span></span>

<span data-ttu-id="277d1-179">主机配置用于 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 实现的属性。</span><span class="sxs-lookup"><span data-stu-id="277d1-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="277d1-180">主机配置可以从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) 获取。</span><span class="sxs-lookup"><span data-stu-id="277d1-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="277d1-181">在 `ConfigureAppConfiguration` 后，`HostBuilderContext.Configuration` 被替换为应用配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="277d1-182">若要添加主机配置，请对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="277d1-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="277d1-183">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="277d1-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="277d1-184">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="277d1-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="277d1-185">CreateDefaultBuilder 包含前缀为 `DOTNET_` 的环境变量提供程序和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="277d1-185">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="277d1-186">对于 web 应用程序，添加前缀为 `ASPNETCORE_` 的环境变量提供程序。</span><span class="sxs-lookup"><span data-stu-id="277d1-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="277d1-187">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="277d1-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="277d1-188">例如，`ASPNETCORE_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="277d1-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="277d1-189">以下示例创建主机配置：</span><span class="sxs-lookup"><span data-stu-id="277d1-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="277d1-190">应用配置</span><span class="sxs-lookup"><span data-stu-id="277d1-190">App configuration</span></span>

<span data-ttu-id="277d1-191">通过对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="277d1-192">可多次调用 `ConfigureAppConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="277d1-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="277d1-193">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="277d1-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="277d1-194">由 `ConfigureAppConfiguration` 创建的配置可以通过 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 获取以用于后续操作，也可以通过 DI 作为服务获取。</span><span class="sxs-lookup"><span data-stu-id="277d1-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="277d1-195">主机配置也会添加到应用配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="277d1-196">有关详细信息，请参阅 [ ASP.NET Core 中的配置](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="277d1-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="277d1-197">适用于所有应用类型的设置</span><span class="sxs-lookup"><span data-stu-id="277d1-197">Settings for all app types</span></span>

<span data-ttu-id="277d1-198">本部分列出了适用于 HTTP 和非 HTTP 工作负荷的主机设置。</span><span class="sxs-lookup"><span data-stu-id="277d1-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="277d1-199">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="277d1-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="277d1-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="277d1-200">ApplicationName</span></span>

<span data-ttu-id="277d1-201">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="277d1-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="277d1-202">**密钥**：applicationName</span><span class="sxs-lookup"><span data-stu-id="277d1-202">**Key**: applicationName</span></span>  
<span data-ttu-id="277d1-203">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-203">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-204">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="277d1-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="277d1-205">**环境变量**：`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="277d1-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="277d1-206">要设置此值，请使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="277d1-206">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="277d1-207">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="277d1-207">ContentRootPath</span></span>

<span data-ttu-id="277d1-208">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 属性决定主机从什么位置开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="277d1-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="277d1-209">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="277d1-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="277d1-210">**键**：contentRoot</span><span class="sxs-lookup"><span data-stu-id="277d1-210">**Key**: contentRoot</span></span>  
<span data-ttu-id="277d1-211">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-211">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-212">**默认**：应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="277d1-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="277d1-213">**环境变量**：`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="277d1-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="277d1-214">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="277d1-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="277d1-215">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="277d1-215">For more information, see:</span></span>

* [<span data-ttu-id="277d1-216">基础知识：内容根目录</span><span class="sxs-lookup"><span data-stu-id="277d1-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="277d1-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="277d1-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="277d1-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="277d1-218">EnvironmentName</span></span>

<span data-ttu-id="277d1-219">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 属性可以设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="277d1-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="277d1-220">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="277d1-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="277d1-221">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="277d1-221">Values aren't case sensitive.</span></span>

<span data-ttu-id="277d1-222">**键**：环境</span><span class="sxs-lookup"><span data-stu-id="277d1-222">**Key**: environment</span></span>  
<span data-ttu-id="277d1-223">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-223">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-224">**默认值**：生产</span><span class="sxs-lookup"><span data-stu-id="277d1-224">**Default**: Production</span></span>  
<span data-ttu-id="277d1-225">**环境变量**：`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="277d1-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="277d1-226">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="277d1-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="277d1-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="277d1-227">ShutdownTimeout</span></span>

<span data-ttu-id="277d1-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时。</span><span class="sxs-lookup"><span data-stu-id="277d1-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="277d1-229">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="277d1-229">The default value is five seconds.</span></span>  <span data-ttu-id="277d1-230">在超时时间段中，主机：</span><span class="sxs-lookup"><span data-stu-id="277d1-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="277d1-231">触发 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="277d1-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="277d1-232">尝试停止托管服务，对服务停止失败的错误进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="277d1-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="277d1-233">如果在所有托管服务停止之前就达到了超时时间，则会在应用关闭时会终止剩余的所有活动的服务。</span><span class="sxs-lookup"><span data-stu-id="277d1-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="277d1-234">即使没有完成处理工作，服务也会停止。</span><span class="sxs-lookup"><span data-stu-id="277d1-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="277d1-235">如果停止服务需要额外的时间，请增加超时时间。</span><span class="sxs-lookup"><span data-stu-id="277d1-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="277d1-236">**键**：shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="277d1-236">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="277d1-237">**类型**：int </span><span class="sxs-lookup"><span data-stu-id="277d1-237">**Type**: *int*</span></span>  
<span data-ttu-id="277d1-238">**默认**：5 秒 **环境变量**：`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="277d1-238">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="277d1-239">若要设置此值，请使用环境变量或配置 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="277d1-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="277d1-240">以下示例将超时设置为 20 秒：</span><span class="sxs-lookup"><span data-stu-id="277d1-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="277d1-241">适用于 Web 应用的设置</span><span class="sxs-lookup"><span data-stu-id="277d1-241">Settings for web apps</span></span>

<span data-ttu-id="277d1-242">一些主机设置仅适用于 HTTP 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="277d1-242">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="277d1-243">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="277d1-243">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="277d1-244">`IWebHostBuilder` 上的扩展方法适用于这些设置。</span><span class="sxs-lookup"><span data-stu-id="277d1-244">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="277d1-245">显示如何调用扩展方法的示例代码假定 `webBuilder` 是 `IWebHostBuilder` 的实例，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="277d1-245">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="277d1-246">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="277d1-246">CaptureStartupErrors</span></span>

<span data-ttu-id="277d1-247">当 `false` 时，启动期间出错导致主机退出。</span><span class="sxs-lookup"><span data-stu-id="277d1-247">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="277d1-248">当 `true` 时，主机在启动期间捕获异常并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="277d1-248">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="277d1-249">**键**：captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="277d1-249">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="277d1-250">**类型**：布尔型（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="277d1-250">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="277d1-251">**默认值**：默认为 `false`，除非应用使用 Kestrel 在 IIS 后方运行，其中默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="277d1-251">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="277d1-252">**环境变量**：`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="277d1-252">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="277d1-253">若要设置此值，使用配置或调用 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="277d1-253">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="277d1-254">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="277d1-254">DetailedErrors</span></span>

<span data-ttu-id="277d1-255">如果启用，或环境为 `Development`，应用会捕获详细错误。</span><span class="sxs-lookup"><span data-stu-id="277d1-255">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="277d1-256">**键**：detailedErrors</span><span class="sxs-lookup"><span data-stu-id="277d1-256">**Key**: detailedErrors</span></span>  
<span data-ttu-id="277d1-257">**类型**：布尔型（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="277d1-257">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="277d1-258">**默认值**：false</span><span class="sxs-lookup"><span data-stu-id="277d1-258">**Default**: false</span></span>  
<span data-ttu-id="277d1-259">**环境变量**：`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="277d1-259">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="277d1-260">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="277d1-260">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="277d1-261">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="277d1-261">HostingStartupAssemblies</span></span>

<span data-ttu-id="277d1-262">承载启动程序集的以分号分隔的字符串在启动时加载。</span><span class="sxs-lookup"><span data-stu-id="277d1-262">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="277d1-263">虽然配置值默认为空字符串，但是承载启动程序集会始终包含应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="277d1-263">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="277d1-264">提供承载启动程序集时，当应用在启动过程中生成其公用服务时将它们添加到应用的程序集加载。</span><span class="sxs-lookup"><span data-stu-id="277d1-264">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="277d1-265">**键**：hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="277d1-265">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="277d1-266">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-266">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-267">**默认值**：空字符串</span><span class="sxs-lookup"><span data-stu-id="277d1-267">**Default**: Empty string</span></span>  
<span data-ttu-id="277d1-268">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="277d1-268">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="277d1-269">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="277d1-269">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="277d1-270">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="277d1-270">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="277d1-271">承载启动程序集的以分号分隔的字符串在启动时排除。</span><span class="sxs-lookup"><span data-stu-id="277d1-271">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="277d1-272">键  ：hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="277d1-272">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="277d1-273">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-273">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-274">**默认值**：空字符串</span><span class="sxs-lookup"><span data-stu-id="277d1-274">**Default**: Empty string</span></span>  
<span data-ttu-id="277d1-275">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="277d1-275">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="277d1-276">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="277d1-276">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="277d1-277">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="277d1-277">HTTPS_Port</span></span>

<span data-ttu-id="277d1-278">HTTPS 重定向端口。</span><span class="sxs-lookup"><span data-stu-id="277d1-278">The HTTPS redirect port.</span></span> <span data-ttu-id="277d1-279">用于[强制实施 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="277d1-279">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="277d1-280">**密钥**：https_port</span><span class="sxs-lookup"><span data-stu-id="277d1-280">**Key**: https_port</span></span>  
<span data-ttu-id="277d1-281">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-281">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-282">**默认**：未设置默认值。</span><span class="sxs-lookup"><span data-stu-id="277d1-282">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="277d1-283">**环境变量**：`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="277d1-283">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="277d1-284">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="277d1-284">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="277d1-285">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="277d1-285">PreferHostingUrls</span></span>

<span data-ttu-id="277d1-286">指示主机是否应该侦听使用 `IWebHostBuilder` 配置的 URL，而不是使用 `IServer` 实现配置的 URL。</span><span class="sxs-lookup"><span data-stu-id="277d1-286">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="277d1-287">**键**：preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="277d1-287">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="277d1-288">**类型**：布尔型（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="277d1-288">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="277d1-289">**默认值**：true</span><span class="sxs-lookup"><span data-stu-id="277d1-289">**Default**: true</span></span>  
<span data-ttu-id="277d1-290">**环境变量**：`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="277d1-290">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="277d1-291">若要设置此值，请使用环境变量或调用 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="277d1-291">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="277d1-292">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="277d1-292">PreventHostingStartup</span></span>

<span data-ttu-id="277d1-293">阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="277d1-293">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="277d1-294">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="277d1-294">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="277d1-295">**键**：preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="277d1-295">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="277d1-296">**类型**：布尔型（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="277d1-296">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="277d1-297">**默认值**：false</span><span class="sxs-lookup"><span data-stu-id="277d1-297">**Default**: false</span></span>  
<span data-ttu-id="277d1-298">**环境变量**：`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="277d1-298">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="277d1-299">若要设置此值，请使用环境变量或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="277d1-299">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="277d1-300">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="277d1-300">StartupAssembly</span></span>

<span data-ttu-id="277d1-301">要搜索 `Startup` 类的程序集。</span><span class="sxs-lookup"><span data-stu-id="277d1-301">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="277d1-302">**键**：startupAssembly</span><span class="sxs-lookup"><span data-stu-id="277d1-302">**Key**: startupAssembly</span></span>  
<span data-ttu-id="277d1-303">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-303">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-304">**默认值**：应用的程序集</span><span class="sxs-lookup"><span data-stu-id="277d1-304">**Default**: The app's assembly</span></span>  
<span data-ttu-id="277d1-305">**环境变量**：`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="277d1-305">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="277d1-306">若要设置此值，请使用环境变量或调用 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="277d1-306">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="277d1-307">`UseStartup` 可以采用程序集名称 (`string`) 或类型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="277d1-307">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="277d1-308">如果调用多个 `UseStartup` 方法，优先选择最后一个方法。</span><span class="sxs-lookup"><span data-stu-id="277d1-308">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="277d1-309">URL</span><span class="sxs-lookup"><span data-stu-id="277d1-309">URLs</span></span>

<span data-ttu-id="277d1-310">IP 地址或主机地址的分号分隔列表，其中包含服务器应针对请求侦听的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="277d1-310">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="277d1-311">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="277d1-311">For example, `http://localhost:123`.</span></span> <span data-ttu-id="277d1-312">使用“\*”指示服务器应针对请求侦听的使用特定端口和协议（例如 `http://*:5000`）的 IP 地址或主机名。</span><span class="sxs-lookup"><span data-stu-id="277d1-312">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="277d1-313">协议（`http://` 或 `https://`）必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="277d1-313">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="277d1-314">不同的服务器支持的格式有所不同。</span><span class="sxs-lookup"><span data-stu-id="277d1-314">Supported formats vary among servers.</span></span>

<span data-ttu-id="277d1-315">**键**：urls</span><span class="sxs-lookup"><span data-stu-id="277d1-315">**Key**: urls</span></span>  
<span data-ttu-id="277d1-316">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-316">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-317">**默认值**：`http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="277d1-317">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="277d1-318">**环境变量**：`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="277d1-318">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="277d1-319">若要设置此值，请使用环境变量或调用 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="277d1-319">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="277d1-320">Kestrel 具有自己的终结点配置 API。</span><span class="sxs-lookup"><span data-stu-id="277d1-320">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="277d1-321">有关详细信息，请参阅 <xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="277d1-321">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="277d1-322">WebRoot</span><span class="sxs-lookup"><span data-stu-id="277d1-322">WebRoot</span></span>

<span data-ttu-id="277d1-323">应用的静态资产的相对路径。</span><span class="sxs-lookup"><span data-stu-id="277d1-323">The relative path to the app's static assets.</span></span>

<span data-ttu-id="277d1-324">**键**：webroot</span><span class="sxs-lookup"><span data-stu-id="277d1-324">**Key**: webroot</span></span>  
<span data-ttu-id="277d1-325">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-325">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-326">**默认**：默认值为 `wwwroot`。</span><span class="sxs-lookup"><span data-stu-id="277d1-326">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="277d1-327">{content root}/wwwroot 的路径必须存在  。</span><span class="sxs-lookup"><span data-stu-id="277d1-327">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="277d1-328">如果该路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="277d1-328">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="277d1-329">**环境变量**：`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="277d1-329">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="277d1-330">若要设置此值，请使用环境变量或调用 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="277d1-330">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="277d1-331">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="277d1-331">For more information, see:</span></span>

* [<span data-ttu-id="277d1-332">基础知识：Web 根目录</span><span class="sxs-lookup"><span data-stu-id="277d1-332">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="277d1-333">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="277d1-333">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="277d1-334">管理主机生存期</span><span class="sxs-lookup"><span data-stu-id="277d1-334">Manage the host lifetime</span></span>

<span data-ttu-id="277d1-335">对生成的 <xref:Microsoft.Extensions.Hosting.IHost> 实现调用方法，以启动和停止应用。</span><span class="sxs-lookup"><span data-stu-id="277d1-335">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="277d1-336">这些方法会影响所有在服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="277d1-336">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="277d1-337">运行</span><span class="sxs-lookup"><span data-stu-id="277d1-337">Run</span></span>

<span data-ttu-id="277d1-338"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机。</span><span class="sxs-lookup"><span data-stu-id="277d1-338"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="277d1-339">RunAsync</span><span class="sxs-lookup"><span data-stu-id="277d1-339">RunAsync</span></span>

<span data-ttu-id="277d1-340"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="277d1-340"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="277d1-341">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="277d1-341">RunConsoleAsync</span></span>

<span data-ttu-id="277d1-342"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 Ctrl+C/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="277d1-342"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="277d1-343">Start</span><span class="sxs-lookup"><span data-stu-id="277d1-343">Start</span></span>

<span data-ttu-id="277d1-344"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="277d1-344"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="277d1-345">StartAsync</span><span class="sxs-lookup"><span data-stu-id="277d1-345">StartAsync</span></span>

<span data-ttu-id="277d1-346"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动主机并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="277d1-346"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="277d1-347">在 `StartAsync` 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="277d1-347"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="277d1-348">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="277d1-348">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="277d1-349">StopAsync</span><span class="sxs-lookup"><span data-stu-id="277d1-349">StopAsync</span></span>

<span data-ttu-id="277d1-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="277d1-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="277d1-351">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="277d1-351">WaitForShutdown</span></span>

<span data-ttu-id="277d1-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 阻止调用线程，直到 IHostLifetime 触发关闭，例如通过 Ctrl+C/SIGINT 或 SIGTERM。</span><span class="sxs-lookup"><span data-stu-id="277d1-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="277d1-353">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="277d1-353">WaitForShutdownAsync</span></span>

<span data-ttu-id="277d1-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="277d1-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="277d1-355">外部控件</span><span class="sxs-lookup"><span data-stu-id="277d1-355">External control</span></span>

<span data-ttu-id="277d1-356">使用可从外部调用的方法，能够实现对主机生存期的直接控制：</span><span class="sxs-lookup"><span data-stu-id="277d1-356">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="277d1-357">ASP.NET Core 应用配置和启动主机。</span><span class="sxs-lookup"><span data-stu-id="277d1-357">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="277d1-358">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="277d1-358">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="277d1-359">本文介绍 ASP.NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)，该主机用于无法处理 HTTP 请求的应用。</span><span class="sxs-lookup"><span data-stu-id="277d1-359">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="277d1-360">泛型主机的用途是将 HTTP 管道从 Web 主机 API 中分离出来，从而启用更多的主机方案。</span><span class="sxs-lookup"><span data-stu-id="277d1-360">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="277d1-361">基于泛型主机的消息、后台任务和其他非 HTTP 工作负载可从横切功能（如配置、依赖关系注入 [DI] 和日志记录）中受益。</span><span class="sxs-lookup"><span data-stu-id="277d1-361">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="277d1-362">泛型主机是 ASP.NET Core 2.1 中的新增功能，不适用于 Web 承载方案。</span><span class="sxs-lookup"><span data-stu-id="277d1-362">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="277d1-363">对于 Web 承载方案，请使用 [Web 主机](xref:fundamentals/host/web-host)。</span><span class="sxs-lookup"><span data-stu-id="277d1-363">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="277d1-364">泛型主机将在未来版本中替换 Web 主机，并在 HTTP 和非 HTTP 方案中充当主要的主机 API。</span><span class="sxs-lookup"><span data-stu-id="277d1-364">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="277d1-365">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="277d1-365">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="277d1-366">在 [Visual Studio Code](https://code.visualstudio.com/) 中运行示例应用时，请使用外部或集成终端  。</span><span class="sxs-lookup"><span data-stu-id="277d1-366">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="277d1-367">请勿在 `internalConsole` 中运行示例。</span><span class="sxs-lookup"><span data-stu-id="277d1-367">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="277d1-368">在 Visual Studio Code 中设置控制台：</span><span class="sxs-lookup"><span data-stu-id="277d1-368">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="277d1-369">打开 .vscode/launch.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="277d1-369">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="277d1-370">在 .NET Core 启动（控制台）配置中，找到控制台条目   。</span><span class="sxs-lookup"><span data-stu-id="277d1-370">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="277d1-371">将值设置为 `externalTerminal` 或 `integratedTerminal`。</span><span class="sxs-lookup"><span data-stu-id="277d1-371">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="277d1-372">介绍</span><span class="sxs-lookup"><span data-stu-id="277d1-372">Introduction</span></span>

<span data-ttu-id="277d1-373">通用主机库位于 <xref:Microsoft.Extensions.Hosting> 命名空间中，由 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="277d1-373">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="277d1-374">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)（ASP.NET Core 2.1 或更高版本）中包括 `Microsoft.Extensions.Hosting` 包。</span><span class="sxs-lookup"><span data-stu-id="277d1-374">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="277d1-375"><xref:Microsoft.Extensions.Hosting.IHostedService> 是执行代码的入口点。</span><span class="sxs-lookup"><span data-stu-id="277d1-375"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="277d1-376">每个 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现都按照 [ConfigureServices 中服务注册](#configureservices)的顺序执行。</span><span class="sxs-lookup"><span data-stu-id="277d1-376">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="277d1-377">主机启动时，每个 <xref:Microsoft.Extensions.Hosting.IHostedService> 上都会调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>，主机正常关闭时，以反向注册顺序调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="277d1-377"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="277d1-378">设置主机</span><span class="sxs-lookup"><span data-stu-id="277d1-378">Set up a host</span></span>

<span data-ttu-id="277d1-379"><xref:Microsoft.Extensions.Hosting.IHostBuilder> 是供库和应用初始化、生成和运行主机的主要组件：</span><span class="sxs-lookup"><span data-stu-id="277d1-379"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="277d1-380">选项</span><span class="sxs-lookup"><span data-stu-id="277d1-380">Options</span></span>

<span data-ttu-id="277d1-381"><xref:Microsoft.Extensions.Hosting.HostOptions> 配置 <xref:Microsoft.Extensions.Hosting.IHost> 的选项。</span><span class="sxs-lookup"><span data-stu-id="277d1-381"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="277d1-382">关闭超时值</span><span class="sxs-lookup"><span data-stu-id="277d1-382">Shutdown timeout</span></span>

<span data-ttu-id="277d1-383"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时值。</span><span class="sxs-lookup"><span data-stu-id="277d1-383"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="277d1-384">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="277d1-384">The default value is five seconds.</span></span>

<span data-ttu-id="277d1-385">`Program.Main` 中的以下选项配置将默认值为 5 秒的关闭超时值增加至 20 秒：</span><span class="sxs-lookup"><span data-stu-id="277d1-385">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="277d1-386">默认服务</span><span class="sxs-lookup"><span data-stu-id="277d1-386">Default services</span></span>

<span data-ttu-id="277d1-387">在主机初始化期间注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="277d1-387">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="277d1-388">[环境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="277d1-388">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="277d1-389">[配置](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="277d1-389">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="277d1-390"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="277d1-390"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="277d1-391"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="277d1-391"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="277d1-392">[选项](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="277d1-392">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="277d1-393">[日志记录](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="277d1-393">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="277d1-394">主机配置</span><span class="sxs-lookup"><span data-stu-id="277d1-394">Host configuration</span></span>

<span data-ttu-id="277d1-395">主机配置的创建方式如下：</span><span class="sxs-lookup"><span data-stu-id="277d1-395">Host configuration is created by:</span></span>

* <span data-ttu-id="277d1-396">调用 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的扩展方法以设置[“内容根”](#content-root)和[“环境”](#environment)。</span><span class="sxs-lookup"><span data-stu-id="277d1-396">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="277d1-397">从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中的配置提供程序读取配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-397">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="277d1-398">扩展方法</span><span class="sxs-lookup"><span data-stu-id="277d1-398">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="277d1-399">应用程序键（名称）</span><span class="sxs-lookup"><span data-stu-id="277d1-399">Application key (name)</span></span>

<span data-ttu-id="277d1-400">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="277d1-400">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="277d1-401">要显式设置值，请使用 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)：</span><span class="sxs-lookup"><span data-stu-id="277d1-401">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="277d1-402">**密钥**：applicationName</span><span class="sxs-lookup"><span data-stu-id="277d1-402">**Key**: applicationName</span></span>  
<span data-ttu-id="277d1-403">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-403">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-404">**默认值**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="277d1-404">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="277d1-405">**设置使用**：`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="277d1-405">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="277d1-406">**环境变量**：`<PREFIX_>APPLICATIONNAME`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="277d1-406">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="277d1-407">内容根</span><span class="sxs-lookup"><span data-stu-id="277d1-407">Content root</span></span>

<span data-ttu-id="277d1-408">此设置确定主机从哪里开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="277d1-408">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="277d1-409">**键**：contentRoot</span><span class="sxs-lookup"><span data-stu-id="277d1-409">**Key**: contentRoot</span></span>  
<span data-ttu-id="277d1-410">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-410">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-411">**默认值**：默认为应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="277d1-411">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="277d1-412">**设置使用**：`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="277d1-412">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="277d1-413">**环境变量**：`<PREFIX_>CONTENTROOT`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="277d1-413">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="277d1-414">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="277d1-414">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="277d1-415">有关详细信息，请参阅[基础知识：内容根目录](xref:fundamentals/index#content-root)。</span><span class="sxs-lookup"><span data-stu-id="277d1-415">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="277d1-416">环境</span><span class="sxs-lookup"><span data-stu-id="277d1-416">Environment</span></span>

<span data-ttu-id="277d1-417">设置应用的[环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="277d1-417">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="277d1-418">**键**：环境</span><span class="sxs-lookup"><span data-stu-id="277d1-418">**Key**: environment</span></span>  
<span data-ttu-id="277d1-419">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="277d1-419">**Type**: *string*</span></span>  
<span data-ttu-id="277d1-420">**默认值**：生产</span><span class="sxs-lookup"><span data-stu-id="277d1-420">**Default**: Production</span></span>  
<span data-ttu-id="277d1-421">**设置使用**：`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="277d1-421">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="277d1-422">**环境变量**：`<PREFIX_>ENVIRONMENT`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="277d1-422">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="277d1-423">可将环境设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="277d1-423">The environment can be set to any value.</span></span> <span data-ttu-id="277d1-424">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="277d1-424">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="277d1-425">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="277d1-425">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="277d1-426">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="277d1-426">ConfigureHostConfiguration</span></span>

<span data-ttu-id="277d1-427"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 来为主机创建 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="277d1-427"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="277d1-428">主机配置用于初始化 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>，以供在应用的构建过程中使用。</span><span class="sxs-lookup"><span data-stu-id="277d1-428">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="277d1-429">可多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="277d1-429"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="277d1-430">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="277d1-430">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="277d1-431">默认情况下不包括提供程序。</span><span class="sxs-lookup"><span data-stu-id="277d1-431">No providers are included by default.</span></span> <span data-ttu-id="277d1-432">必须在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中显式指定应用所需的任何配置提供程序，包括：</span><span class="sxs-lookup"><span data-stu-id="277d1-432">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="277d1-433">文件配置（例如，来自 hostsettings.json 文件）  。</span><span class="sxs-lookup"><span data-stu-id="277d1-433">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="277d1-434">环境变量配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-434">Environment variable configuration.</span></span>
* <span data-ttu-id="277d1-435">命令行参数配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-435">Command-line argument configuration.</span></span>
* <span data-ttu-id="277d1-436">任何其他所需的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="277d1-436">Any other required configuration providers.</span></span>

<span data-ttu-id="277d1-437">通过使用 `SetBasePath` 指定应用的基本路径，然后调用其中一个[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)，可以启用主机的文件配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-437">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="277d1-438">示例应用使用 JSON 文件 hostsettings.json，并调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 来使用文件的主机配置设置  。</span><span class="sxs-lookup"><span data-stu-id="277d1-438">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="277d1-439">要添加主机的[环境变量配置](xref:fundamentals/configuration/index#environment-variables-configuration-provider)，请在主机生成器上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>。</span><span class="sxs-lookup"><span data-stu-id="277d1-439">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="277d1-440">`AddEnvironmentVariables` 接受用户定义的前缀（可选）。</span><span class="sxs-lookup"><span data-stu-id="277d1-440">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="277d1-441">示例应用使用前缀 `PREFIX_`。</span><span class="sxs-lookup"><span data-stu-id="277d1-441">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="277d1-442">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="277d1-442">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="277d1-443">配置示例应用的主机后，`PREFIX_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="277d1-443">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="277d1-444">在开发过程中，如果使用 [Visual Studio](https://visualstudio.microsoft.com) 或通过 `dotnet run` 运行应用，可能会在 Properties/launchSettings.json  文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="277d1-444">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="277d1-445">若在开发过程中使用 [Visual Studio Code](https://code.visualstudio.com/)，可能会在 .vscode/launch.json  文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="277d1-445">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="277d1-446">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="277d1-446">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="277d1-447">通过调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 可添加[命令行配置](xref:fundamentals/configuration/index#command-line-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="277d1-447">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="277d1-448">最后添加命令行配置以允许命令行参数替代之前配置提供程序提供的配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-448">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="277d1-449">hostsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="277d1-449">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="277d1-450">可以通过 [applicationName](#application-key-name) 和 [contentRoot](#content-root) 键提供其他配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-450">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="277d1-451">示例 `HostBuilder` 配置使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="277d1-451">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="277d1-452">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="277d1-452">ConfigureAppConfiguration</span></span>

<span data-ttu-id="277d1-453">通过在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 实现上调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="277d1-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 来为应用创建 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="277d1-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="277d1-455">可多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="277d1-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="277d1-456">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="277d1-456">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="277d1-457">[HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 中提供 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建的配置，以供进行后续操作和在 <xref:Microsoft.Extensions.Hosting.IHost.Services*> 中使用。</span><span class="sxs-lookup"><span data-stu-id="277d1-457">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="277d1-458">应用配置会自动接收 [ConfigureHostConfiguration](#configurehostconfiguration) 提供的主机配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-458">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="277d1-459">示例应用配置使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="277d1-459">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="277d1-460">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="277d1-460">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="277d1-461">appsettings.Development.json  ：</span><span class="sxs-lookup"><span data-stu-id="277d1-461">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="277d1-462">appsettings.Production.json  ：</span><span class="sxs-lookup"><span data-stu-id="277d1-462">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="277d1-463">要将设置文件移动到输出目录，请在项目文件中将设置文件指定为 [MSBuild 项目项](/visualstudio/msbuild/common-msbuild-project-items)。</span><span class="sxs-lookup"><span data-stu-id="277d1-463">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="277d1-464">示例应用移动具有以下 `<Content>` 项的 JSON 应用设置文件和 hostsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="277d1-464">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="277d1-465">配置扩展方法（如 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 和 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>）需要其他 NuGet 包（如 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) 和 [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)）。</span><span class="sxs-lookup"><span data-stu-id="277d1-465">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="277d1-466">除非应用使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，否则除了核心 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) 包之外，还必须将这些包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="277d1-466">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="277d1-467">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="277d1-467">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="277d1-468">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="277d1-468">ConfigureServices</span></span>

<span data-ttu-id="277d1-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 将服务添加到应用的[依赖关系](xref:fundamentals/dependency-injection)注入容器。</span><span class="sxs-lookup"><span data-stu-id="277d1-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="277d1-470">可多次调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="277d1-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="277d1-471">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="277d1-471">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="277d1-472">有关详细信息，请参阅 <xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="277d1-472">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="277d1-473">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)使用 `AddHostedService` 扩展方法向应用添加生存期事件 `LifetimeEventsHostedService` 和定时后台任务 `TimedHostedService` 服务：</span><span class="sxs-lookup"><span data-stu-id="277d1-473">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="277d1-474">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="277d1-474">ConfigureLogging</span></span>

<span data-ttu-id="277d1-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 添加了一个委托来配置提供的 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>。</span><span class="sxs-lookup"><span data-stu-id="277d1-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="277d1-476">可以利用相加结果多次调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="277d1-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="277d1-477">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="277d1-477">UseConsoleLifetime</span></span>

<span data-ttu-id="277d1-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 侦听 Ctrl+C/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="277d1-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="277d1-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 解除阻止 [ RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="277d1-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="277d1-480"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> 预注册为默认生存期实现。</span><span class="sxs-lookup"><span data-stu-id="277d1-480"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="277d1-481">使用注册的最后一个生存期。</span><span class="sxs-lookup"><span data-stu-id="277d1-481">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="277d1-482">容器配置</span><span class="sxs-lookup"><span data-stu-id="277d1-482">Container configuration</span></span>

<span data-ttu-id="277d1-483">为支持插入其他容器中，主机可以接受 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>。</span><span class="sxs-lookup"><span data-stu-id="277d1-483">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="277d1-484">提供工厂不属于 DI 容器注册，而是用于创建具体 DI 容器的主机内部函数。</span><span class="sxs-lookup"><span data-stu-id="277d1-484">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="277d1-485">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) 重写用于创建应用的服务提供程序的默认工厂。</span><span class="sxs-lookup"><span data-stu-id="277d1-485">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="277d1-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 方法托管自定义容器配置。</span><span class="sxs-lookup"><span data-stu-id="277d1-486">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="277d1-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 提供在基础主机 API 的基础之上配置容器的强类型体验。</span><span class="sxs-lookup"><span data-stu-id="277d1-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="277d1-488">可以利用相加结果多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>。</span><span class="sxs-lookup"><span data-stu-id="277d1-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="277d1-489">为应用创建服务容器：</span><span class="sxs-lookup"><span data-stu-id="277d1-489">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="277d1-490">提供服务容器工厂：</span><span class="sxs-lookup"><span data-stu-id="277d1-490">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="277d1-491">使用该工厂并为应用配置自定义服务容器：</span><span class="sxs-lookup"><span data-stu-id="277d1-491">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="277d1-492">扩展性</span><span class="sxs-lookup"><span data-stu-id="277d1-492">Extensibility</span></span>

<span data-ttu-id="277d1-493">在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上使用扩展方法实现主机扩展性。</span><span class="sxs-lookup"><span data-stu-id="277d1-493">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="277d1-494">以下示例介绍扩展方法如何使用 <xref:fundamentals/host/hosted-services> 中所示的 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 示例来扩展 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 实现。</span><span class="sxs-lookup"><span data-stu-id="277d1-494">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="277d1-495">应用建立 `UseHostedService` 扩展方法，以注册在 `T` 中传递的托管服务：</span><span class="sxs-lookup"><span data-stu-id="277d1-495">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="277d1-496">管理主机</span><span class="sxs-lookup"><span data-stu-id="277d1-496">Manage the host</span></span>

<span data-ttu-id="277d1-497"><xref:Microsoft.Extensions.Hosting.IHost> 实现负责启动和停止服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="277d1-497">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="277d1-498">运行</span><span class="sxs-lookup"><span data-stu-id="277d1-498">Run</span></span>

<span data-ttu-id="277d1-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机：</span><span class="sxs-lookup"><span data-stu-id="277d1-499"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="277d1-500">RunAsync</span><span class="sxs-lookup"><span data-stu-id="277d1-500">RunAsync</span></span>

<span data-ttu-id="277d1-501"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="277d1-501"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="277d1-502">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="277d1-502">RunConsoleAsync</span></span>

<span data-ttu-id="277d1-503"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 Ctrl+C/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="277d1-503"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="277d1-504">Start 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="277d1-504">Start and StopAsync</span></span>

<span data-ttu-id="277d1-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="277d1-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="277d1-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="277d1-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="277d1-507">StartAsync 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="277d1-507">StartAsync and StopAsync</span></span>

<span data-ttu-id="277d1-508"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动应用。</span><span class="sxs-lookup"><span data-stu-id="277d1-508"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="277d1-509"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 停止应用。</span><span class="sxs-lookup"><span data-stu-id="277d1-509"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="277d1-510">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="277d1-510">WaitForShutdown</span></span>

<span data-ttu-id="277d1-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 通过 <xref:Microsoft.Extensions.Hosting.IHostLifetime> 触发，例如 <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>（侦听 Ctrl+C/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="277d1-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="277d1-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="277d1-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="277d1-513">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="277d1-513">WaitForShutdownAsync</span></span>

<span data-ttu-id="277d1-514"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="277d1-514"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="277d1-515">外部控件</span><span class="sxs-lookup"><span data-stu-id="277d1-515">External control</span></span>

<span data-ttu-id="277d1-516">使用可从外部调用的方法，能够实现主机的外部控件：</span><span class="sxs-lookup"><span data-stu-id="277d1-516">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="277d1-517">在 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="277d1-517"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="277d1-518">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="277d1-518">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="277d1-519">IHostingEnvironment 接口</span><span class="sxs-lookup"><span data-stu-id="277d1-519">IHostingEnvironment interface</span></span>

<span data-ttu-id="277d1-520"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 提供有关应用托管环境的信息。</span><span class="sxs-lookup"><span data-stu-id="277d1-520"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="277d1-521">使用[构造函数注入](xref:fundamentals/dependency-injection)获取 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 以使用其属性和扩展方法：</span><span class="sxs-lookup"><span data-stu-id="277d1-521">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="277d1-522">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="277d1-522">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="277d1-523">IApplicationLifetime 接口</span><span class="sxs-lookup"><span data-stu-id="277d1-523">IApplicationLifetime interface</span></span>

<span data-ttu-id="277d1-524"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 允许启动后和关闭活动，包括正常关闭请求。</span><span class="sxs-lookup"><span data-stu-id="277d1-524"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="277d1-525">接口上的三个属性是用于注册 <xref:System.Action> 方法（用于定义启动和关闭事件）的取消标记。</span><span class="sxs-lookup"><span data-stu-id="277d1-525">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="277d1-526">取消标记</span><span class="sxs-lookup"><span data-stu-id="277d1-526">Cancellation Token</span></span> | <span data-ttu-id="277d1-527">触发条件</span><span class="sxs-lookup"><span data-stu-id="277d1-527">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="277d1-528">主机已完全启动。</span><span class="sxs-lookup"><span data-stu-id="277d1-528">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="277d1-529">主机正在完成正常关闭。</span><span class="sxs-lookup"><span data-stu-id="277d1-529">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="277d1-530">应处理所有请求。</span><span class="sxs-lookup"><span data-stu-id="277d1-530">All requests should be processed.</span></span> <span data-ttu-id="277d1-531">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="277d1-531">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="277d1-532">主机正在执行正常关闭。</span><span class="sxs-lookup"><span data-stu-id="277d1-532">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="277d1-533">仍在处理请求。</span><span class="sxs-lookup"><span data-stu-id="277d1-533">Requests may still be processing.</span></span> <span data-ttu-id="277d1-534">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="277d1-534">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="277d1-535">构造函数将 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 服务注入到任何类中。</span><span class="sxs-lookup"><span data-stu-id="277d1-535">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="277d1-536">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)将构造函数注入到 `LifetimeEventsHostedService` 类（一个 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现）中，用于注册事件。</span><span class="sxs-lookup"><span data-stu-id="277d1-536">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="277d1-537">LifetimeEventsHostedService.cs  ：</span><span class="sxs-lookup"><span data-stu-id="277d1-537">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="277d1-538"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 请求终止应用。</span><span class="sxs-lookup"><span data-stu-id="277d1-538"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="277d1-539">以下类在调用类的 `Shutdown` 方法时使用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 正常关闭应用：</span><span class="sxs-lookup"><span data-stu-id="277d1-539">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="277d1-540">其他资源</span><span class="sxs-lookup"><span data-stu-id="277d1-540">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
