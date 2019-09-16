---
title: .NET 通用主机
author: tdykstra
description: 了解 .NET Core 泛型主机，该主机负责应用启动和生存期管理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 261abae499a0d5f807a14aebd224949881067bc7
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773817"
---
# <a name="net-generic-host"></a><span data-ttu-id="a76dc-103">.NET 通用主机</span><span class="sxs-lookup"><span data-stu-id="a76dc-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a76dc-104">本文介绍了 .NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 并提供有关使用方法的指南。</span><span class="sxs-lookup"><span data-stu-id="a76dc-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="a76dc-105">什么是主机？</span><span class="sxs-lookup"><span data-stu-id="a76dc-105">What's a host?</span></span>

<span data-ttu-id="a76dc-106">主机是封装应用资源的对象，例如  ：</span><span class="sxs-lookup"><span data-stu-id="a76dc-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="a76dc-107">依赖关系注入 (DI)</span><span class="sxs-lookup"><span data-stu-id="a76dc-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="a76dc-108">Logging</span><span class="sxs-lookup"><span data-stu-id="a76dc-108">Logging</span></span>
* <span data-ttu-id="a76dc-109">配置</span><span class="sxs-lookup"><span data-stu-id="a76dc-109">Configuration</span></span>
* <span data-ttu-id="a76dc-110">`IHostedService` 实现</span><span class="sxs-lookup"><span data-stu-id="a76dc-110">`IHostedService` implementations</span></span>

<span data-ttu-id="a76dc-111">启动主机时，它对它在 DI 容器中找到的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的每个实现调用 `IHostedService.StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="a76dc-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="a76dc-112">在 web 应用中，其中一个 `IHostedService` 实现是启动 [HTTP 服务器实现](xref:fundamentals/index#servers)的 web 服务。</span><span class="sxs-lookup"><span data-stu-id="a76dc-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="a76dc-113">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="a76dc-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="a76dc-114">在低于 3.0 的 ASP.NET Core 版本中，[Web 主机](xref:fundamentals/host/web-host)用于 HTTP 工作负载。</span><span class="sxs-lookup"><span data-stu-id="a76dc-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="a76dc-115">不再建议将 Web 主机用于 Web 应用，但该主机仍可仅用于后向兼容性。</span><span class="sxs-lookup"><span data-stu-id="a76dc-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="a76dc-116">设置主机</span><span class="sxs-lookup"><span data-stu-id="a76dc-116">Set up a host</span></span>

<span data-ttu-id="a76dc-117">主机通常由 `Program` 类中的代码配置、生成和运行。</span><span class="sxs-lookup"><span data-stu-id="a76dc-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="a76dc-118">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="a76dc-118">The `Main` method:</span></span>

* <span data-ttu-id="a76dc-119">调用 `CreateHostBuilder` 方法以创建和配置生成器对象。</span><span class="sxs-lookup"><span data-stu-id="a76dc-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="a76dc-120">对生成器对象调用 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="a76dc-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="a76dc-121">以下是用于非 HTTP 工作负载的 Program.cs  代码，其中单个 `IHostedService` 实现添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="a76dc-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="a76dc-122">对于 HTTP 工作负荷，`Main` 方法相同，但 `CreateHostBuilder` 调用 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="a76dc-123">如果应用使用 Entity Framework Core，不要更改 `CreateHostBuilder` 方法的名称或签名。</span><span class="sxs-lookup"><span data-stu-id="a76dc-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="a76dc-124">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)应查找一个无需运行应用即可配置主机的 `CreateHostBuilder` 方法。</span><span class="sxs-lookup"><span data-stu-id="a76dc-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="a76dc-125">有关详细信息，请参阅[设计时 DbContext 创建](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="a76dc-126">默认生成器设置</span><span class="sxs-lookup"><span data-stu-id="a76dc-126">Default builder settings</span></span> 

<span data-ttu-id="a76dc-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 方法：</span><span class="sxs-lookup"><span data-stu-id="a76dc-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="a76dc-128">将内容根路径设置为由 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的路径。</span><span class="sxs-lookup"><span data-stu-id="a76dc-128">Sets the content root to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="a76dc-129">通过以下项加载主机配置：</span><span class="sxs-lookup"><span data-stu-id="a76dc-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="a76dc-130">前缀为“DOTNET_”的环境变量。</span><span class="sxs-lookup"><span data-stu-id="a76dc-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="a76dc-131">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="a76dc-131">Command-line arguments.</span></span>
* <span data-ttu-id="a76dc-132">通过以下对象加载应用配置：</span><span class="sxs-lookup"><span data-stu-id="a76dc-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="a76dc-133">appsettings.json  。</span><span class="sxs-lookup"><span data-stu-id="a76dc-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="a76dc-134">appsettings.{Environment}.json  。</span><span class="sxs-lookup"><span data-stu-id="a76dc-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="a76dc-135">[密钥管理器](xref:security/app-secrets) 当应用在 `Development` 环境中运行时。</span><span class="sxs-lookup"><span data-stu-id="a76dc-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="a76dc-136">环境变量。</span><span class="sxs-lookup"><span data-stu-id="a76dc-136">Environment variables.</span></span>
  * <span data-ttu-id="a76dc-137">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="a76dc-137">Command-line arguments.</span></span>
* <span data-ttu-id="a76dc-138">添加以下[日志记录](xref:fundamentals/logging/index)提供程序：</span><span class="sxs-lookup"><span data-stu-id="a76dc-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="a76dc-139">控制台</span><span class="sxs-lookup"><span data-stu-id="a76dc-139">Console</span></span>
  * <span data-ttu-id="a76dc-140">调试</span><span class="sxs-lookup"><span data-stu-id="a76dc-140">Debug</span></span>
  * <span data-ttu-id="a76dc-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="a76dc-141">EventSource</span></span>
  * <span data-ttu-id="a76dc-142">EventLog（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="a76dc-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="a76dc-143">当环境为“开发”时，启用[范围验证](xref:fundamentals/dependency-injection#scope-validation)和[依赖关系验证](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="a76dc-144">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="a76dc-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="a76dc-145">从前缀为“ASPNETCORE_”的环境变量加载主机配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="a76dc-146">使用应用的托管配置提供程序将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器设置为 web 服务器并对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="a76dc-147">有关 Kestrel 服务器默认选项，请参阅 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="a76dc-148">添加[主机筛选中间件](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="a76dc-149">如果 ASPNETCORE_FORWARDEDHEADERS_ENABLED=true，请添加[转发标头中间件](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="a76dc-150">支持 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="a76dc-150">Enables IIS integration.</span></span> <span data-ttu-id="a76dc-151">有关 IIS 默认选项，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="a76dc-152">本文中后面的[所有应用类型的设置](#settings-for-all-app-types)和[ web 应用的设置](#settings-for-web-apps)部分介绍如何替代默认生成器设置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="a76dc-153">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="a76dc-153">Framework-provided services</span></span>

<span data-ttu-id="a76dc-154">自动注册的服务包括：</span><span class="sxs-lookup"><span data-stu-id="a76dc-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="a76dc-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="a76dc-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="a76dc-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="a76dc-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="a76dc-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="a76dc-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="a76dc-158">有关框架提供的所有服务列表，请参阅 <xref:fundamentals/dependency-injection#framework-provided-services>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-158">For a list of all framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="a76dc-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="a76dc-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="a76dc-160">将 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>（以前称为 `IApplicationLifetime`）服务注入任何类以处理启动后和正常关闭任务。</span><span class="sxs-lookup"><span data-stu-id="a76dc-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="a76dc-161">接口上的三个属性是用于注册应用启动和应用停止事件处理程序方法的取消令牌。</span><span class="sxs-lookup"><span data-stu-id="a76dc-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="a76dc-162">该接口还包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="a76dc-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="a76dc-163">以下示例是注册 `IApplicationLifetime` 事件的 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="a76dc-163">The following example is an `IHostedService` implementation that registers the `IApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="a76dc-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="a76dc-164">IHostLifetime</span></span>

<span data-ttu-id="a76dc-165"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 实现控制主机何时启动和何时停止。</span><span class="sxs-lookup"><span data-stu-id="a76dc-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="a76dc-166">使用了已注册的最后一个实现。</span><span class="sxs-lookup"><span data-stu-id="a76dc-166">The last implementation registered is used.</span></span>

<span data-ttu-id="a76dc-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> 是默认的 `IHostLifetime` 实现。</span><span class="sxs-lookup"><span data-stu-id="a76dc-167"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="a76dc-168">`ConsoleLifetime`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="a76dc-169">侦听 Ctrl+C/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="a76dc-169">listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="a76dc-170">解除阻止 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="a76dc-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="a76dc-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="a76dc-171">IHostEnvironment</span></span>

<span data-ttu-id="a76dc-172">将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 服务注册到一个类，获取关于以下项的信息：</span><span class="sxs-lookup"><span data-stu-id="a76dc-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="a76dc-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="a76dc-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="a76dc-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="a76dc-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="a76dc-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="a76dc-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="a76dc-176">Web 应用实现 `IWebHostEnvironment` 接口，该接口继承 `IHostEnvironment` 并添加：</span><span class="sxs-lookup"><span data-stu-id="a76dc-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds:</span></span>

* [<span data-ttu-id="a76dc-177">WebRootPath</span><span class="sxs-lookup"><span data-stu-id="a76dc-177">WebRootPath</span></span>](#webroot)

## <a name="host-configuration"></a><span data-ttu-id="a76dc-178">主机配置</span><span class="sxs-lookup"><span data-stu-id="a76dc-178">Host configuration</span></span>

<span data-ttu-id="a76dc-179">主机配置用于 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 实现的属性。</span><span class="sxs-lookup"><span data-stu-id="a76dc-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="a76dc-180">主机配置可以从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) 获取。</span><span class="sxs-lookup"><span data-stu-id="a76dc-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="a76dc-181">在 `ConfigureAppConfiguration` 后，`HostBuilderContext.Configuration` 被替换为应用配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="a76dc-182">若要添加主机配置，请对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="a76dc-183">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="a76dc-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="a76dc-184">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="a76dc-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="a76dc-185">CreateDefaultBuilder 包含前缀为 `DOTNET_` 的环境变量提供程序和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="a76dc-185">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="a76dc-186">对于 web 应用程序，添加前缀为 `ASPNETCORE_` 的环境变量提供程序。</span><span class="sxs-lookup"><span data-stu-id="a76dc-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="a76dc-187">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="a76dc-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="a76dc-188">例如，`ASPNETCORE_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="a76dc-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="a76dc-189">以下示例创建主机配置：</span><span class="sxs-lookup"><span data-stu-id="a76dc-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="a76dc-190">应用配置</span><span class="sxs-lookup"><span data-stu-id="a76dc-190">App configuration</span></span>

<span data-ttu-id="a76dc-191">通过对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="a76dc-192">可多次调用 `ConfigureAppConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="a76dc-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="a76dc-193">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="a76dc-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="a76dc-194">由 `ConfigureAppConfiguration` 创建的配置可以通过 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 获取以用于后续操作，也可以通过 DI 作为服务获取。</span><span class="sxs-lookup"><span data-stu-id="a76dc-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="a76dc-195">主机配置也会添加到应用配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="a76dc-196">有关详细信息，请参阅 [ ASP.NET Core 中的配置](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="a76dc-197">适用于所有应用类型的设置</span><span class="sxs-lookup"><span data-stu-id="a76dc-197">Settings for all app types</span></span>

<span data-ttu-id="a76dc-198">本部分列出了适用于 HTTP 和非 HTTP 工作负荷的主机设置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="a76dc-199">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="a76dc-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="a76dc-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="a76dc-200">ApplicationName</span></span>

<span data-ttu-id="a76dc-201">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="a76dc-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="a76dc-202">**密钥**：applicationName</span><span class="sxs-lookup"><span data-stu-id="a76dc-202">**Key**: applicationName</span></span>  
<span data-ttu-id="a76dc-203">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-203">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-204">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="a76dc-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="a76dc-205">**环境变量**：`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="a76dc-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="a76dc-206">要设置此值，请使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="a76dc-206">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="a76dc-207">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="a76dc-207">ContentRootPath</span></span>

<span data-ttu-id="a76dc-208">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 属性决定主机从什么位置开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="a76dc-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="a76dc-209">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="a76dc-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="a76dc-210">**键**：contentRoot</span><span class="sxs-lookup"><span data-stu-id="a76dc-210">**Key**: contentRoot</span></span>  
<span data-ttu-id="a76dc-211">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-211">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-212">**默认**：应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="a76dc-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="a76dc-213">**环境变量**：`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="a76dc-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="a76dc-214">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

### <a name="environmentname"></a><span data-ttu-id="a76dc-215">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="a76dc-215">EnvironmentName</span></span>

<span data-ttu-id="a76dc-216">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 属性可以设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="a76dc-216">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="a76dc-217">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="a76dc-217">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="a76dc-218">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="a76dc-218">Values aren't case sensitive.</span></span>

<span data-ttu-id="a76dc-219">**键**：环境</span><span class="sxs-lookup"><span data-stu-id="a76dc-219">**Key**: environment</span></span>  
<span data-ttu-id="a76dc-220">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-220">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-221">**默认值**：生产</span><span class="sxs-lookup"><span data-stu-id="a76dc-221">**Default**: Production</span></span>  
<span data-ttu-id="a76dc-222">**环境变量**：`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="a76dc-222">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="a76dc-223">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-223">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="a76dc-224">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="a76dc-224">ShutdownTimeout</span></span>

<span data-ttu-id="a76dc-225">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时。</span><span class="sxs-lookup"><span data-stu-id="a76dc-225">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="a76dc-226">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="a76dc-226">The default value is five seconds.</span></span>  <span data-ttu-id="a76dc-227">在超时时间段中，主机：</span><span class="sxs-lookup"><span data-stu-id="a76dc-227">During the timeout period, the host:</span></span>

* <span data-ttu-id="a76dc-228">触发 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-228">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="a76dc-229">尝试停止托管服务，对服务停止失败的错误进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="a76dc-229">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="a76dc-230">如果在所有托管服务停止之前就达到了超时时间，则会在应用关闭时会终止剩余的所有活动的服务。</span><span class="sxs-lookup"><span data-stu-id="a76dc-230">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="a76dc-231">即使没有完成处理工作，服务也会停止。</span><span class="sxs-lookup"><span data-stu-id="a76dc-231">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="a76dc-232">如果停止服务需要额外的时间，请增加超时时间。</span><span class="sxs-lookup"><span data-stu-id="a76dc-232">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="a76dc-233">**键**：shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="a76dc-233">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="a76dc-234">**类型**：int </span><span class="sxs-lookup"><span data-stu-id="a76dc-234">**Type**: *int*</span></span>  
<span data-ttu-id="a76dc-235">**默认**：5 秒 **环境变量**：`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="a76dc-235">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="a76dc-236">若要设置此值，请使用环境变量或配置 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="a76dc-236">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="a76dc-237">以下示例将超时设置为 20 秒：</span><span class="sxs-lookup"><span data-stu-id="a76dc-237">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="a76dc-238">适用于 Web 应用的设置</span><span class="sxs-lookup"><span data-stu-id="a76dc-238">Settings for web apps</span></span>

<span data-ttu-id="a76dc-239">一些主机设置仅适用于 HTTP 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="a76dc-239">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="a76dc-240">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="a76dc-240">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="a76dc-241">`IWebHostBuilder` 上的扩展方法适用于这些设置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-241">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="a76dc-242">显示如何调用扩展方法的示例代码假定 `webBuilder` 是 `IWebHostBuilder` 的实例，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="a76dc-242">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="a76dc-243">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="a76dc-243">CaptureStartupErrors</span></span>

<span data-ttu-id="a76dc-244">当 `false` 时，启动期间出错导致主机退出。</span><span class="sxs-lookup"><span data-stu-id="a76dc-244">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="a76dc-245">当 `true` 时，主机在启动期间捕获异常并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="a76dc-245">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="a76dc-246">**键**：captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="a76dc-246">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="a76dc-247">**类型**：布尔型（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="a76dc-247">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a76dc-248">**默认值**：默认为 `false`，除非应用使用 Kestrel 在 IIS 后方运行，其中默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="a76dc-248">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="a76dc-249">**环境变量**：`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="a76dc-249">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="a76dc-250">若要设置此值，使用配置或调用 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-250">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="a76dc-251">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="a76dc-251">DetailedErrors</span></span>

<span data-ttu-id="a76dc-252">如果启用，或环境为 `Development`，应用会捕获详细错误。</span><span class="sxs-lookup"><span data-stu-id="a76dc-252">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="a76dc-253">**键**：detailedErrors</span><span class="sxs-lookup"><span data-stu-id="a76dc-253">**Key**: detailedErrors</span></span>  
<span data-ttu-id="a76dc-254">**类型**：布尔型（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="a76dc-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a76dc-255">**默认值**：false</span><span class="sxs-lookup"><span data-stu-id="a76dc-255">**Default**: false</span></span>  
<span data-ttu-id="a76dc-256">**环境变量**：`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="a76dc-256">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="a76dc-257">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-257">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="a76dc-258">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="a76dc-258">HostingStartupAssemblies</span></span>

<span data-ttu-id="a76dc-259">承载启动程序集的以分号分隔的字符串在启动时加载。</span><span class="sxs-lookup"><span data-stu-id="a76dc-259">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="a76dc-260">虽然配置值默认为空字符串，但是承载启动程序集会始终包含应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="a76dc-260">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="a76dc-261">提供承载启动程序集时，当应用在启动过程中生成其公用服务时将它们添加到应用的程序集加载。</span><span class="sxs-lookup"><span data-stu-id="a76dc-261">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="a76dc-262">**键**：hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="a76dc-262">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="a76dc-263">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-263">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-264">**默认值**：空字符串</span><span class="sxs-lookup"><span data-stu-id="a76dc-264">**Default**: Empty string</span></span>  
<span data-ttu-id="a76dc-265">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a76dc-265">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="a76dc-266">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-266">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="a76dc-267">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="a76dc-267">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="a76dc-268">承载启动程序集的以分号分隔的字符串在启动时排除。</span><span class="sxs-lookup"><span data-stu-id="a76dc-268">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="a76dc-269">键  ：hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="a76dc-269">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="a76dc-270">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-270">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-271">**默认值**：空字符串</span><span class="sxs-lookup"><span data-stu-id="a76dc-271">**Default**: Empty string</span></span>  
<span data-ttu-id="a76dc-272">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="a76dc-272">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="a76dc-273">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="a76dc-274">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="a76dc-274">HTTPS_Port</span></span>

<span data-ttu-id="a76dc-275">HTTPS 重定向端口。</span><span class="sxs-lookup"><span data-stu-id="a76dc-275">The HTTPS redirect port.</span></span> <span data-ttu-id="a76dc-276">用于[强制实施 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-276">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="a76dc-277">**密钥**：https_port</span><span class="sxs-lookup"><span data-stu-id="a76dc-277">**Key**: https_port</span></span>  
<span data-ttu-id="a76dc-278">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-278">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-279">**默认**：未设置默认值。</span><span class="sxs-lookup"><span data-stu-id="a76dc-279">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="a76dc-280">**环境变量**：`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="a76dc-280">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="a76dc-281">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="a76dc-282">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="a76dc-282">PreferHostingUrls</span></span>

<span data-ttu-id="a76dc-283">指示主机是否应该侦听使用 `IWebHostBuilder` 配置的 URL，而不是使用 `IServer` 实现配置的 URL。</span><span class="sxs-lookup"><span data-stu-id="a76dc-283">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="a76dc-284">**键**：preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="a76dc-284">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="a76dc-285">**类型**：布尔型（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="a76dc-285">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a76dc-286">**默认值**：true</span><span class="sxs-lookup"><span data-stu-id="a76dc-286">**Default**: true</span></span>  
<span data-ttu-id="a76dc-287">**环境变量**：`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="a76dc-287">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="a76dc-288">若要设置此值，请使用环境变量或调用 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-288">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="a76dc-289">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="a76dc-289">PreventHostingStartup</span></span>

<span data-ttu-id="a76dc-290">阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="a76dc-290">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="a76dc-291">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-291">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="a76dc-292">**键**：preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="a76dc-292">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="a76dc-293">**类型**：布尔型（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="a76dc-293">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="a76dc-294">**默认值**：false</span><span class="sxs-lookup"><span data-stu-id="a76dc-294">**Default**: false</span></span>  
<span data-ttu-id="a76dc-295">**环境变量**：`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="a76dc-295">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="a76dc-296">若要设置此值，请使用环境变量或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-296">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="a76dc-297">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="a76dc-297">StartupAssembly</span></span>

<span data-ttu-id="a76dc-298">要搜索 `Startup` 类的程序集。</span><span class="sxs-lookup"><span data-stu-id="a76dc-298">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="a76dc-299">**键**：startupAssembly</span><span class="sxs-lookup"><span data-stu-id="a76dc-299">**Key**: startupAssembly</span></span>  
<span data-ttu-id="a76dc-300">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-300">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-301">**默认值**：应用的程序集</span><span class="sxs-lookup"><span data-stu-id="a76dc-301">**Default**: The app's assembly</span></span>  
<span data-ttu-id="a76dc-302">**环境变量**：`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="a76dc-302">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="a76dc-303">若要设置此值，请使用环境变量或调用 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="a76dc-303">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="a76dc-304">`UseStartup` 可以采用程序集名称 (`string`) 或类型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-304">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="a76dc-305">如果调用多个 `UseStartup` 方法，优先选择最后一个方法。</span><span class="sxs-lookup"><span data-stu-id="a76dc-305">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="a76dc-306">URL</span><span class="sxs-lookup"><span data-stu-id="a76dc-306">URLs</span></span>

<span data-ttu-id="a76dc-307">IP 地址或主机地址的分号分隔列表，其中包含服务器应针对请求侦听的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="a76dc-307">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="a76dc-308">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="a76dc-308">For example, `http://localhost:123`.</span></span> <span data-ttu-id="a76dc-309">使用“\*”指示服务器应针对请求侦听的使用特定端口和协议（例如 `http://*:5000`）的 IP 地址或主机名。</span><span class="sxs-lookup"><span data-stu-id="a76dc-309">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="a76dc-310">协议（`http://` 或 `https://`）必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="a76dc-310">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="a76dc-311">不同的服务器支持的格式有所不同。</span><span class="sxs-lookup"><span data-stu-id="a76dc-311">Supported formats vary among servers.</span></span>

<span data-ttu-id="a76dc-312">**键**：urls</span><span class="sxs-lookup"><span data-stu-id="a76dc-312">**Key**: urls</span></span>  
<span data-ttu-id="a76dc-313">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-313">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-314">**默认值**：`http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="a76dc-314">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="a76dc-315">**环境变量**：`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="a76dc-315">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="a76dc-316">若要设置此值，请使用环境变量或调用 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-316">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="a76dc-317">Kestrel 具有自己的终结点配置 API。</span><span class="sxs-lookup"><span data-stu-id="a76dc-317">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="a76dc-318">有关详细信息，请参阅 <xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-318">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="a76dc-319">WebRoot</span><span class="sxs-lookup"><span data-stu-id="a76dc-319">WebRoot</span></span>

<span data-ttu-id="a76dc-320">应用的静态资产的相对路径。</span><span class="sxs-lookup"><span data-stu-id="a76dc-320">The relative path to the app's static assets.</span></span>

<span data-ttu-id="a76dc-321">**键**：webroot</span><span class="sxs-lookup"><span data-stu-id="a76dc-321">**Key**: webroot</span></span>  
<span data-ttu-id="a76dc-322">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-322">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-323">**默认**：(内容根路径)/wwwroot，如果路径存在  。</span><span class="sxs-lookup"><span data-stu-id="a76dc-323">**Default**: *(Content Root)/wwwroot*, if the path exists.</span></span> <span data-ttu-id="a76dc-324">如果该路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="a76dc-324">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="a76dc-325">**环境变量**：`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="a76dc-325">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="a76dc-326">若要设置此值，请使用环境变量或调用 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="a76dc-326">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="a76dc-327">管理主机生存期</span><span class="sxs-lookup"><span data-stu-id="a76dc-327">Manage the host lifetime</span></span>

<span data-ttu-id="a76dc-328">对生成的 <xref:Microsoft.Extensions.Hosting.IHost> 实现调用方法，以启动和停止应用。</span><span class="sxs-lookup"><span data-stu-id="a76dc-328">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="a76dc-329">这些方法会影响所有在服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="a76dc-329">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="a76dc-330">运行</span><span class="sxs-lookup"><span data-stu-id="a76dc-330">Run</span></span>

<span data-ttu-id="a76dc-331"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机。</span><span class="sxs-lookup"><span data-stu-id="a76dc-331"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="a76dc-332">RunAsync</span><span class="sxs-lookup"><span data-stu-id="a76dc-332">RunAsync</span></span>

<span data-ttu-id="a76dc-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-333"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="a76dc-334">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="a76dc-334">RunConsoleAsync</span></span>

<span data-ttu-id="a76dc-335"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 Ctrl+C/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="a76dc-335"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="a76dc-336">Start</span><span class="sxs-lookup"><span data-stu-id="a76dc-336">Start</span></span>

<span data-ttu-id="a76dc-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="a76dc-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="a76dc-338">StartAsync</span><span class="sxs-lookup"><span data-stu-id="a76dc-338">StartAsync</span></span>

<span data-ttu-id="a76dc-339"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动主机并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-339"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="a76dc-340">在 `StartAsync` 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="a76dc-340"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="a76dc-341">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="a76dc-341">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="a76dc-342">StopAsync</span><span class="sxs-lookup"><span data-stu-id="a76dc-342">StopAsync</span></span>

<span data-ttu-id="a76dc-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="a76dc-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="a76dc-344">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="a76dc-344">WaitForShutdown</span></span>

<span data-ttu-id="a76dc-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 阻止调用线程，直到 IHostLifetime 触发关闭，例如通过 Ctrl+C/SIGINT 或 SIGTERM。</span><span class="sxs-lookup"><span data-stu-id="a76dc-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="a76dc-346">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="a76dc-346">WaitForShutdownAsync</span></span>

<span data-ttu-id="a76dc-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-347"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="a76dc-348">外部控件</span><span class="sxs-lookup"><span data-stu-id="a76dc-348">External control</span></span>

<span data-ttu-id="a76dc-349">使用可从外部调用的方法，能够实现对主机生存期的直接控制：</span><span class="sxs-lookup"><span data-stu-id="a76dc-349">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="a76dc-350">ASP.NET Core 应用配置和启动主机。</span><span class="sxs-lookup"><span data-stu-id="a76dc-350">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="a76dc-351">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="a76dc-351">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="a76dc-352">本文介绍 ASP.NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)，该主机用于无法处理 HTTP 请求的应用。</span><span class="sxs-lookup"><span data-stu-id="a76dc-352">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="a76dc-353">泛型主机的用途是将 HTTP 管道从 Web 主机 API 中分离出来，从而启用更多的主机方案。</span><span class="sxs-lookup"><span data-stu-id="a76dc-353">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="a76dc-354">基于泛型主机的消息、后台任务和其他非 HTTP 工作负载可从横切功能（如配置、依赖关系注入 [DI] 和日志记录）中受益。</span><span class="sxs-lookup"><span data-stu-id="a76dc-354">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="a76dc-355">泛型主机是 ASP.NET Core 2.1 中的新增功能，不适用于 Web 承载方案。</span><span class="sxs-lookup"><span data-stu-id="a76dc-355">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="a76dc-356">对于 Web 承载方案，请使用 [Web 主机](xref:fundamentals/host/web-host)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-356">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="a76dc-357">泛型主机将在未来版本中替换 Web 主机，并在 HTTP 和非 HTTP 方案中充当主要的主机 API。</span><span class="sxs-lookup"><span data-stu-id="a76dc-357">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="a76dc-358">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a76dc-358">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a76dc-359">在 [Visual Studio Code](https://code.visualstudio.com/) 中运行示例应用时，请使用外部或集成终端  。</span><span class="sxs-lookup"><span data-stu-id="a76dc-359">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="a76dc-360">请勿在 `internalConsole` 中运行示例。</span><span class="sxs-lookup"><span data-stu-id="a76dc-360">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="a76dc-361">在 Visual Studio Code 中设置控制台：</span><span class="sxs-lookup"><span data-stu-id="a76dc-361">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="a76dc-362">打开 .vscode/launch.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="a76dc-362">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="a76dc-363">在 .NET Core 启动（控制台）配置中，找到控制台条目   。</span><span class="sxs-lookup"><span data-stu-id="a76dc-363">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="a76dc-364">将值设置为 `externalTerminal` 或 `integratedTerminal`。</span><span class="sxs-lookup"><span data-stu-id="a76dc-364">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="a76dc-365">介绍</span><span class="sxs-lookup"><span data-stu-id="a76dc-365">Introduction</span></span>

<span data-ttu-id="a76dc-366">通用主机库位于 <xref:Microsoft.Extensions.Hosting> 命名空间中，由 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="a76dc-366">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="a76dc-367">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)（ASP.NET Core 2.1 或更高版本）中包括 `Microsoft.Extensions.Hosting` 包。</span><span class="sxs-lookup"><span data-stu-id="a76dc-367">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="a76dc-368"><xref:Microsoft.Extensions.Hosting.IHostedService> 是执行代码的入口点。</span><span class="sxs-lookup"><span data-stu-id="a76dc-368"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="a76dc-369">每个 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现都按照 [ConfigureServices 中服务注册](#configureservices)的顺序执行。</span><span class="sxs-lookup"><span data-stu-id="a76dc-369">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="a76dc-370">主机启动时，每个 <xref:Microsoft.Extensions.Hosting.IHostedService> 上都会调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>，主机正常关闭时，以反向注册顺序调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-370"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="a76dc-371">设置主机</span><span class="sxs-lookup"><span data-stu-id="a76dc-371">Set up a host</span></span>

<span data-ttu-id="a76dc-372"><xref:Microsoft.Extensions.Hosting.IHostBuilder> 是供库和应用初始化、生成和运行主机的主要组件：</span><span class="sxs-lookup"><span data-stu-id="a76dc-372"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="a76dc-373">选项</span><span class="sxs-lookup"><span data-stu-id="a76dc-373">Options</span></span>

<span data-ttu-id="a76dc-374"><xref:Microsoft.Extensions.Hosting.HostOptions> 配置 <xref:Microsoft.Extensions.Hosting.IHost> 的选项。</span><span class="sxs-lookup"><span data-stu-id="a76dc-374"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="a76dc-375">关闭超时值</span><span class="sxs-lookup"><span data-stu-id="a76dc-375">Shutdown timeout</span></span>

<span data-ttu-id="a76dc-376"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时值。</span><span class="sxs-lookup"><span data-stu-id="a76dc-376"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="a76dc-377">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="a76dc-377">The default value is five seconds.</span></span>

<span data-ttu-id="a76dc-378">`Program.Main` 中的以下选项配置将默认值为 5 秒的关闭超时值增加至 20 秒：</span><span class="sxs-lookup"><span data-stu-id="a76dc-378">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="a76dc-379">默认服务</span><span class="sxs-lookup"><span data-stu-id="a76dc-379">Default services</span></span>

<span data-ttu-id="a76dc-380">在主机初始化期间注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="a76dc-380">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="a76dc-381">[环境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="a76dc-381">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="a76dc-382">[配置](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="a76dc-382">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="a76dc-383"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span><span class="sxs-lookup"><span data-stu-id="a76dc-383"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)</span></span>
* <span data-ttu-id="a76dc-384"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span><span class="sxs-lookup"><span data-stu-id="a76dc-384"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="a76dc-385">[选项](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="a76dc-385">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="a76dc-386">[日志记录](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="a76dc-386">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="a76dc-387">主机配置</span><span class="sxs-lookup"><span data-stu-id="a76dc-387">Host configuration</span></span>

<span data-ttu-id="a76dc-388">主机配置的创建方式如下：</span><span class="sxs-lookup"><span data-stu-id="a76dc-388">Host configuration is created by:</span></span>

* <span data-ttu-id="a76dc-389">调用 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的扩展方法以设置[“内容根”](#content-root)和[“环境”](#environment)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-389">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="a76dc-390">从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中的配置提供程序读取配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-390">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="a76dc-391">扩展方法</span><span class="sxs-lookup"><span data-stu-id="a76dc-391">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="a76dc-392">应用程序键（名称）</span><span class="sxs-lookup"><span data-stu-id="a76dc-392">Application key (name)</span></span>

<span data-ttu-id="a76dc-393">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="a76dc-393">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="a76dc-394">要显式设置值，请使用 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)：</span><span class="sxs-lookup"><span data-stu-id="a76dc-394">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="a76dc-395">**密钥**：applicationName</span><span class="sxs-lookup"><span data-stu-id="a76dc-395">**Key**: applicationName</span></span>  
<span data-ttu-id="a76dc-396">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-396">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-397">**默认值**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="a76dc-397">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="a76dc-398">**设置使用**：`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="a76dc-398">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="a76dc-399">**环境变量**：`<PREFIX_>APPLICATIONNAME`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="a76dc-399">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="a76dc-400">内容根</span><span class="sxs-lookup"><span data-stu-id="a76dc-400">Content root</span></span>

<span data-ttu-id="a76dc-401">此设置确定主机从哪里开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="a76dc-401">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="a76dc-402">**键**：contentRoot</span><span class="sxs-lookup"><span data-stu-id="a76dc-402">**Key**: contentRoot</span></span>  
<span data-ttu-id="a76dc-403">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-403">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-404">**默认值**：默认为应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="a76dc-404">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="a76dc-405">**设置使用**：`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="a76dc-405">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="a76dc-406">**环境变量**：`<PREFIX_>CONTENTROOT`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="a76dc-406">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="a76dc-407">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="a76dc-407">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a><span data-ttu-id="a76dc-408">环境</span><span class="sxs-lookup"><span data-stu-id="a76dc-408">Environment</span></span>

<span data-ttu-id="a76dc-409">设置应用的[环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-409">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="a76dc-410">**键**：环境</span><span class="sxs-lookup"><span data-stu-id="a76dc-410">**Key**: environment</span></span>  
<span data-ttu-id="a76dc-411">**类型**：string </span><span class="sxs-lookup"><span data-stu-id="a76dc-411">**Type**: *string*</span></span>  
<span data-ttu-id="a76dc-412">**默认值**：生产</span><span class="sxs-lookup"><span data-stu-id="a76dc-412">**Default**: Production</span></span>  
<span data-ttu-id="a76dc-413">**设置使用**：`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="a76dc-413">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="a76dc-414">**环境变量**：`<PREFIX_>ENVIRONMENT`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="a76dc-414">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="a76dc-415">可将环境设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="a76dc-415">The environment can be set to any value.</span></span> <span data-ttu-id="a76dc-416">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="a76dc-416">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="a76dc-417">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="a76dc-417">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="a76dc-418">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="a76dc-418">ConfigureHostConfiguration</span></span>

<span data-ttu-id="a76dc-419"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 来为主机创建 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-419"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="a76dc-420">主机配置用于初始化 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>，以供在应用的构建过程中使用。</span><span class="sxs-lookup"><span data-stu-id="a76dc-420">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="a76dc-421">可多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="a76dc-421"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="a76dc-422">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="a76dc-422">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="a76dc-423">默认情况下不包括提供程序。</span><span class="sxs-lookup"><span data-stu-id="a76dc-423">No providers are included by default.</span></span> <span data-ttu-id="a76dc-424">必须在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中显式指定应用所需的任何配置提供程序，包括：</span><span class="sxs-lookup"><span data-stu-id="a76dc-424">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="a76dc-425">文件配置（例如，来自 hostsettings.json 文件）  。</span><span class="sxs-lookup"><span data-stu-id="a76dc-425">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="a76dc-426">环境变量配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-426">Environment variable configuration.</span></span>
* <span data-ttu-id="a76dc-427">命令行参数配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-427">Command-line argument configuration.</span></span>
* <span data-ttu-id="a76dc-428">任何其他所需的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="a76dc-428">Any other required configuration providers.</span></span>

<span data-ttu-id="a76dc-429">通过使用 `SetBasePath` 指定应用的基本路径，然后调用其中一个[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)，可以启用主机的文件配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-429">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="a76dc-430">示例应用使用 JSON 文件 hostsettings.json，并调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 来使用文件的主机配置设置  。</span><span class="sxs-lookup"><span data-stu-id="a76dc-430">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="a76dc-431">要添加主机的[环境变量配置](xref:fundamentals/configuration/index#environment-variables-configuration-provider)，请在主机生成器上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-431">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="a76dc-432">`AddEnvironmentVariables` 接受用户定义的前缀（可选）。</span><span class="sxs-lookup"><span data-stu-id="a76dc-432">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="a76dc-433">示例应用使用前缀 `PREFIX_`。</span><span class="sxs-lookup"><span data-stu-id="a76dc-433">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="a76dc-434">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="a76dc-434">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="a76dc-435">配置示例应用的主机后，`PREFIX_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="a76dc-435">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="a76dc-436">在开发过程中，如果使用 [Visual Studio](https://visualstudio.microsoft.com) 或通过 `dotnet run` 运行应用，可能会在 Properties/launchSettings.json  文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a76dc-436">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="a76dc-437">若在开发过程中使用 [Visual Studio Code](https://code.visualstudio.com/)，可能会在 .vscode/launch.json  文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="a76dc-437">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="a76dc-438">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-438">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a76dc-439">通过调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 可添加[命令行配置](xref:fundamentals/configuration/index#command-line-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-439">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="a76dc-440">最后添加命令行配置以允许命令行参数替代之前配置提供程序提供的配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-440">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="a76dc-441">hostsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="a76dc-441">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="a76dc-442">可以通过 [applicationName](#application-key-name) 和 [contentRoot](#content-root) 键提供其他配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-442">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="a76dc-443">示例 `HostBuilder` 配置使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="a76dc-443">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="a76dc-444">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="a76dc-444">ConfigureAppConfiguration</span></span>

<span data-ttu-id="a76dc-445">通过在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 实现上调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-445">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="a76dc-446"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 来为应用创建 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-446"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="a76dc-447">可多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="a76dc-447"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="a76dc-448">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="a76dc-448">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="a76dc-449">[HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 中提供 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建的配置，以供进行后续操作和在 <xref:Microsoft.Extensions.Hosting.IHost.Services*> 中使用。</span><span class="sxs-lookup"><span data-stu-id="a76dc-449">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="a76dc-450">应用配置会自动接收 [ConfigureHostConfiguration](#configurehostconfiguration) 提供的主机配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-450">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="a76dc-451">示例应用配置使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="a76dc-451">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="a76dc-452">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="a76dc-452">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="a76dc-453">appsettings.Development.json  ：</span><span class="sxs-lookup"><span data-stu-id="a76dc-453">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="a76dc-454">appsettings.Production.json  ：</span><span class="sxs-lookup"><span data-stu-id="a76dc-454">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="a76dc-455">要将设置文件移动到输出目录，请在项目文件中将设置文件指定为 [MSBuild 项目项](/visualstudio/msbuild/common-msbuild-project-items)。</span><span class="sxs-lookup"><span data-stu-id="a76dc-455">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="a76dc-456">示例应用移动具有以下 `<Content>` 项的 JSON 应用设置文件和 hostsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="a76dc-456">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="a76dc-457">配置扩展方法（如 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 和 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>）需要其他 NuGet 包（如 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) 和 [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)）。</span><span class="sxs-lookup"><span data-stu-id="a76dc-457">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="a76dc-458">除非应用使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，否则除了核心 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) 包之外，还必须将这些包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="a76dc-458">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="a76dc-459">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-459">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="a76dc-460">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="a76dc-460">ConfigureServices</span></span>

<span data-ttu-id="a76dc-461"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 将服务添加到应用的[依赖关系](xref:fundamentals/dependency-injection)注入容器。</span><span class="sxs-lookup"><span data-stu-id="a76dc-461"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a76dc-462">可多次调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="a76dc-462"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="a76dc-463">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="a76dc-463">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a76dc-464">有关详细信息，请参阅 <xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-464">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="a76dc-465">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)使用 `AddHostedService` 扩展方法向应用添加生存期事件 `LifetimeEventsHostedService` 和定时后台任务 `TimedHostedService` 服务：</span><span class="sxs-lookup"><span data-stu-id="a76dc-465">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="a76dc-466">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="a76dc-466">ConfigureLogging</span></span>

<span data-ttu-id="a76dc-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 添加了一个委托来配置提供的 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-467"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="a76dc-468">可以利用相加结果多次调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="a76dc-469">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="a76dc-469">UseConsoleLifetime</span></span>

<span data-ttu-id="a76dc-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 侦听 Ctrl+C/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="a76dc-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="a76dc-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 解除阻止 [ RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="a76dc-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="a76dc-472"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> 预注册为默认生存期实现。</span><span class="sxs-lookup"><span data-stu-id="a76dc-472"><xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="a76dc-473">使用注册的最后一个生存期。</span><span class="sxs-lookup"><span data-stu-id="a76dc-473">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="a76dc-474">容器配置</span><span class="sxs-lookup"><span data-stu-id="a76dc-474">Container configuration</span></span>

<span data-ttu-id="a76dc-475">为支持插入其他容器中，主机可以接受 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-475">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="a76dc-476">提供工厂不属于 DI 容器注册，而是用于创建具体 DI 容器的主机内部函数。</span><span class="sxs-lookup"><span data-stu-id="a76dc-476">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="a76dc-477">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) 重写用于创建应用的服务提供程序的默认工厂。</span><span class="sxs-lookup"><span data-stu-id="a76dc-477">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="a76dc-478"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 方法托管自定义容器配置。</span><span class="sxs-lookup"><span data-stu-id="a76dc-478">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="a76dc-479"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 提供在基础主机 API 的基础之上配置容器的强类型体验。</span><span class="sxs-lookup"><span data-stu-id="a76dc-479"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="a76dc-480">可以利用相加结果多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-480"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="a76dc-481">为应用创建服务容器：</span><span class="sxs-lookup"><span data-stu-id="a76dc-481">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="a76dc-482">提供服务容器工厂：</span><span class="sxs-lookup"><span data-stu-id="a76dc-482">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="a76dc-483">使用该工厂并为应用配置自定义服务容器：</span><span class="sxs-lookup"><span data-stu-id="a76dc-483">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="a76dc-484">扩展性</span><span class="sxs-lookup"><span data-stu-id="a76dc-484">Extensibility</span></span>

<span data-ttu-id="a76dc-485">在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上使用扩展方法实现主机扩展性。</span><span class="sxs-lookup"><span data-stu-id="a76dc-485">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="a76dc-486">以下示例介绍扩展方法如何使用 <xref:fundamentals/host/hosted-services> 中所示的 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 示例来扩展 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 实现。</span><span class="sxs-lookup"><span data-stu-id="a76dc-486">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="a76dc-487">应用建立 `UseHostedService` 扩展方法，以注册在 `T` 中传递的托管服务：</span><span class="sxs-lookup"><span data-stu-id="a76dc-487">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="a76dc-488">管理主机</span><span class="sxs-lookup"><span data-stu-id="a76dc-488">Manage the host</span></span>

<span data-ttu-id="a76dc-489"><xref:Microsoft.Extensions.Hosting.IHost> 实现负责启动和停止服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="a76dc-489">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="a76dc-490">运行</span><span class="sxs-lookup"><span data-stu-id="a76dc-490">Run</span></span>

<span data-ttu-id="a76dc-491"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机：</span><span class="sxs-lookup"><span data-stu-id="a76dc-491"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="a76dc-492">RunAsync</span><span class="sxs-lookup"><span data-stu-id="a76dc-492">RunAsync</span></span>

<span data-ttu-id="a76dc-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="a76dc-493"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="a76dc-494">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="a76dc-494">RunConsoleAsync</span></span>

<span data-ttu-id="a76dc-495"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 Ctrl+C/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="a76dc-495"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="a76dc-496">Start 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="a76dc-496">Start and StopAsync</span></span>

<span data-ttu-id="a76dc-497"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="a76dc-497"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="a76dc-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="a76dc-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="a76dc-499">StartAsync 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="a76dc-499">StartAsync and StopAsync</span></span>

<span data-ttu-id="a76dc-500"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动应用。</span><span class="sxs-lookup"><span data-stu-id="a76dc-500"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="a76dc-501"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 停止应用。</span><span class="sxs-lookup"><span data-stu-id="a76dc-501"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="a76dc-502">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="a76dc-502">WaitForShutdown</span></span>

<span data-ttu-id="a76dc-503"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 通过 <xref:Microsoft.Extensions.Hosting.IHostLifetime> 触发，例如 <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>（侦听 Ctrl+C/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="a76dc-503"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="a76dc-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="a76dc-505">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="a76dc-505">WaitForShutdownAsync</span></span>

<span data-ttu-id="a76dc-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="a76dc-507">外部控件</span><span class="sxs-lookup"><span data-stu-id="a76dc-507">External control</span></span>

<span data-ttu-id="a76dc-508">使用可从外部调用的方法，能够实现主机的外部控件：</span><span class="sxs-lookup"><span data-stu-id="a76dc-508">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="a76dc-509">在 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="a76dc-509"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="a76dc-510">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="a76dc-510">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="a76dc-511">IHostingEnvironment 接口</span><span class="sxs-lookup"><span data-stu-id="a76dc-511">IHostingEnvironment interface</span></span>

<span data-ttu-id="a76dc-512"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 提供有关应用托管环境的信息。</span><span class="sxs-lookup"><span data-stu-id="a76dc-512"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="a76dc-513">使用[构造函数注入](xref:fundamentals/dependency-injection)获取 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 以使用其属性和扩展方法：</span><span class="sxs-lookup"><span data-stu-id="a76dc-513">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="a76dc-514">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="a76dc-514">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="a76dc-515">IApplicationLifetime 接口</span><span class="sxs-lookup"><span data-stu-id="a76dc-515">IApplicationLifetime interface</span></span>

<span data-ttu-id="a76dc-516"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 允许启动后和关闭活动，包括正常关闭请求。</span><span class="sxs-lookup"><span data-stu-id="a76dc-516"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="a76dc-517">接口上的三个属性是用于注册 <xref:System.Action> 方法（用于定义启动和关闭事件）的取消标记。</span><span class="sxs-lookup"><span data-stu-id="a76dc-517">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="a76dc-518">取消标记</span><span class="sxs-lookup"><span data-stu-id="a76dc-518">Cancellation Token</span></span> | <span data-ttu-id="a76dc-519">触发条件</span><span class="sxs-lookup"><span data-stu-id="a76dc-519">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="a76dc-520">主机已完全启动。</span><span class="sxs-lookup"><span data-stu-id="a76dc-520">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="a76dc-521">主机正在完成正常关闭。</span><span class="sxs-lookup"><span data-stu-id="a76dc-521">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="a76dc-522">应处理所有请求。</span><span class="sxs-lookup"><span data-stu-id="a76dc-522">All requests should be processed.</span></span> <span data-ttu-id="a76dc-523">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="a76dc-523">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="a76dc-524">主机正在执行正常关闭。</span><span class="sxs-lookup"><span data-stu-id="a76dc-524">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="a76dc-525">仍在处理请求。</span><span class="sxs-lookup"><span data-stu-id="a76dc-525">Requests may still be processing.</span></span> <span data-ttu-id="a76dc-526">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="a76dc-526">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="a76dc-527">构造函数将 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 服务注入到任何类中。</span><span class="sxs-lookup"><span data-stu-id="a76dc-527">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="a76dc-528">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)将构造函数注入到 `LifetimeEventsHostedService` 类（一个 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现）中，用于注册事件。</span><span class="sxs-lookup"><span data-stu-id="a76dc-528">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="a76dc-529">LifetimeEventsHostedService.cs  ：</span><span class="sxs-lookup"><span data-stu-id="a76dc-529">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="a76dc-530"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 请求终止应用。</span><span class="sxs-lookup"><span data-stu-id="a76dc-530"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="a76dc-531">以下类在调用类的 `Shutdown` 方法时使用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 正常关闭应用：</span><span class="sxs-lookup"><span data-stu-id="a76dc-531">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a76dc-532">其他资源</span><span class="sxs-lookup"><span data-stu-id="a76dc-532">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
