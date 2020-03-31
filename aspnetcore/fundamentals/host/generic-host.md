---
title: .NET 通用主机
author: rick-anderson
description: 了解 .NET Core 泛型主机，该主机负责应用启动和生存期管理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 0f8f03dabf65f2cbfe4c41d36b02a25d7902cefb
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219215"
---
# <a name="net-generic-host"></a><span data-ttu-id="3ab35-103">.NET 通用主机</span><span class="sxs-lookup"><span data-stu-id="3ab35-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3ab35-104">本文介绍了 .NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 并提供有关使用方法的指南。</span><span class="sxs-lookup"><span data-stu-id="3ab35-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="3ab35-105">什么是主机？</span><span class="sxs-lookup"><span data-stu-id="3ab35-105">What's a host?</span></span>

<span data-ttu-id="3ab35-106">主机是封装应用资源的对象，例如  ：</span><span class="sxs-lookup"><span data-stu-id="3ab35-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="3ab35-107">依赖关系注入 (DI)</span><span class="sxs-lookup"><span data-stu-id="3ab35-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="3ab35-108">Logging</span><span class="sxs-lookup"><span data-stu-id="3ab35-108">Logging</span></span>
* <span data-ttu-id="3ab35-109">Configuration</span><span class="sxs-lookup"><span data-stu-id="3ab35-109">Configuration</span></span>
* <span data-ttu-id="3ab35-110">`IHostedService` 实现</span><span class="sxs-lookup"><span data-stu-id="3ab35-110">`IHostedService` implementations</span></span>

<span data-ttu-id="3ab35-111">启动主机时，它对它在 DI 容器中找到的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的每个实现调用 `IHostedService.StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="3ab35-112">在 web 应用中，其中一个 `IHostedService` 实现是启动 [HTTP 服务器实现](xref:fundamentals/index#servers)的 web 服务。</span><span class="sxs-lookup"><span data-stu-id="3ab35-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="3ab35-113">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="3ab35-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="3ab35-114">在低于 3.0 的 ASP.NET Core 版本中，[Web 主机](xref:fundamentals/host/web-host)用于 HTTP 工作负载。</span><span class="sxs-lookup"><span data-stu-id="3ab35-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="3ab35-115">不再建议将 Web 主机用于 Web 应用，但该主机仍可仅用于后向兼容性。</span><span class="sxs-lookup"><span data-stu-id="3ab35-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="3ab35-116">设置主机</span><span class="sxs-lookup"><span data-stu-id="3ab35-116">Set up a host</span></span>

<span data-ttu-id="3ab35-117">主机通常由 `Program` 类中的代码配置、生成和运行。</span><span class="sxs-lookup"><span data-stu-id="3ab35-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="3ab35-118">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="3ab35-118">The `Main` method:</span></span>

* <span data-ttu-id="3ab35-119">调用 `CreateHostBuilder` 方法以创建和配置生成器对象。</span><span class="sxs-lookup"><span data-stu-id="3ab35-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="3ab35-120">对生成器对象调用 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ab35-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="3ab35-121">以下是用于非 HTTP 工作负载的 Program.cs  代码，其中单个 `IHostedService` 实现添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="3ab35-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="3ab35-122">对于 HTTP 工作负荷，`Main` 方法相同，但 `CreateHostBuilder` 调用 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="3ab35-123">如果应用使用 Entity Framework Core，不要更改 `CreateHostBuilder` 方法的名称或签名。</span><span class="sxs-lookup"><span data-stu-id="3ab35-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="3ab35-124">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)应查找一个无需运行应用即可配置主机的 `CreateHostBuilder` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ab35-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="3ab35-125">有关详细信息，请参阅[设计时 DbContext 创建](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="3ab35-126">默认生成器设置</span><span class="sxs-lookup"><span data-stu-id="3ab35-126">Default builder settings</span></span>

<span data-ttu-id="3ab35-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 方法：</span><span class="sxs-lookup"><span data-stu-id="3ab35-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="3ab35-128">将[内容根目录](xref:fundamentals/index#content-root)设置为由 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的路径。</span><span class="sxs-lookup"><span data-stu-id="3ab35-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="3ab35-129">通过以下项加载主机配置：</span><span class="sxs-lookup"><span data-stu-id="3ab35-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="3ab35-130">前缀为 `DOTNET_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="3ab35-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="3ab35-131">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3ab35-131">Command-line arguments.</span></span>
* <span data-ttu-id="3ab35-132">通过以下对象加载应用配置：</span><span class="sxs-lookup"><span data-stu-id="3ab35-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="3ab35-133">appsettings.json  。</span><span class="sxs-lookup"><span data-stu-id="3ab35-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="3ab35-134">appsettings.{Environment}.json  。</span><span class="sxs-lookup"><span data-stu-id="3ab35-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="3ab35-135">[密钥管理器](xref:security/app-secrets) 当应用在 `Development` 环境中运行时。</span><span class="sxs-lookup"><span data-stu-id="3ab35-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="3ab35-136">环境变量。</span><span class="sxs-lookup"><span data-stu-id="3ab35-136">Environment variables.</span></span>
  * <span data-ttu-id="3ab35-137">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3ab35-137">Command-line arguments.</span></span>
* <span data-ttu-id="3ab35-138">添加以下[日志记录](xref:fundamentals/logging/index)提供程序：</span><span class="sxs-lookup"><span data-stu-id="3ab35-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="3ab35-139">控制台</span><span class="sxs-lookup"><span data-stu-id="3ab35-139">Console</span></span>
  * <span data-ttu-id="3ab35-140">调试</span><span class="sxs-lookup"><span data-stu-id="3ab35-140">Debug</span></span>
  * <span data-ttu-id="3ab35-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="3ab35-141">EventSource</span></span>
  * <span data-ttu-id="3ab35-142">EventLog（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="3ab35-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="3ab35-143">当环境为“开发”时，启用[范围验证](xref:fundamentals/dependency-injection#scope-validation)和[依赖关系验证](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="3ab35-144">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="3ab35-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="3ab35-145">从前缀为 `ASPNETCORE_` 的环境变量加载主机配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="3ab35-146">使用应用的托管配置提供程序将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器设置为 web 服务器并对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="3ab35-147">有关 Kestrel 服务器默认选项，请参阅 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="3ab35-148">添加[主机筛选中间件](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="3ab35-149">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 等于 `true`，则添加[转接头中间件](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="3ab35-150">支持 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="3ab35-150">Enables IIS integration.</span></span> <span data-ttu-id="3ab35-151">有关 IIS 默认选项，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="3ab35-152">本文中后面的[所有应用类型的设置](#settings-for-all-app-types)和[ web 应用的设置](#settings-for-web-apps)部分介绍如何替代默认生成器设置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="3ab35-153">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="3ab35-153">Framework-provided services</span></span>

<span data-ttu-id="3ab35-154">自动注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="3ab35-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="3ab35-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3ab35-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="3ab35-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3ab35-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="3ab35-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3ab35-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="3ab35-158">有关框架提供的服务的详细信息，请参阅 <xref:fundamentals/dependency-injection#framework-provided-services>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="3ab35-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3ab35-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="3ab35-160">将 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>（以前称为 `IApplicationLifetime`）服务注入任何类以处理启动后和正常关闭任务。</span><span class="sxs-lookup"><span data-stu-id="3ab35-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="3ab35-161">接口上的三个属性是用于注册应用启动和应用停止事件处理程序方法的取消令牌。</span><span class="sxs-lookup"><span data-stu-id="3ab35-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="3ab35-162">该接口还包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ab35-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="3ab35-163">以下示例是注册 `IHostApplicationLifetime` 事件的 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="3ab35-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="3ab35-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3ab35-164">IHostLifetime</span></span>

<span data-ttu-id="3ab35-165"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 实现控制主机何时启动和何时停止。</span><span class="sxs-lookup"><span data-stu-id="3ab35-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="3ab35-166">使用了已注册的最后一个实现。</span><span class="sxs-lookup"><span data-stu-id="3ab35-166">The last implementation registered is used.</span></span>

<span data-ttu-id="3ab35-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是默认的 `IHostLifetime` 实现。</span><span class="sxs-lookup"><span data-stu-id="3ab35-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="3ab35-168">`ConsoleLifetime`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="3ab35-169">侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="3ab35-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="3ab35-170">解除阻止 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="3ab35-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="3ab35-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3ab35-171">IHostEnvironment</span></span>

<span data-ttu-id="3ab35-172">将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 服务注册到一个类，获取关于以下设置的信息：</span><span class="sxs-lookup"><span data-stu-id="3ab35-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="3ab35-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="3ab35-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="3ab35-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="3ab35-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="3ab35-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3ab35-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="3ab35-176">Web 应用实现 `IWebHostEnvironment` 接口，该接口继承 `IHostEnvironment` 并添加 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="3ab35-177">主机配置</span><span class="sxs-lookup"><span data-stu-id="3ab35-177">Host configuration</span></span>

<span data-ttu-id="3ab35-178">主机配置用于 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 实现的属性。</span><span class="sxs-lookup"><span data-stu-id="3ab35-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="3ab35-179">主机配置可以从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) 获取。</span><span class="sxs-lookup"><span data-stu-id="3ab35-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="3ab35-180">在 `ConfigureAppConfiguration` 后，`HostBuilderContext.Configuration` 被替换为应用配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="3ab35-181">若要添加主机配置，请对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3ab35-182">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="3ab35-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3ab35-183">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="3ab35-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="3ab35-184">`CreateDefaultBuilder` 包含前缀为 `DOTNET_` 的环境变量提供程序和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3ab35-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3ab35-185">对于 web 应用程序，添加前缀为 `ASPNETCORE_` 的环境变量提供程序。</span><span class="sxs-lookup"><span data-stu-id="3ab35-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="3ab35-186">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="3ab35-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="3ab35-187">例如，`ASPNETCORE_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="3ab35-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="3ab35-188">以下示例创建主机配置：</span><span class="sxs-lookup"><span data-stu-id="3ab35-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="3ab35-189">应用配置</span><span class="sxs-lookup"><span data-stu-id="3ab35-189">App configuration</span></span>

<span data-ttu-id="3ab35-190">通过对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3ab35-191">可多次调用 `ConfigureAppConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="3ab35-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3ab35-192">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="3ab35-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="3ab35-193">由 `ConfigureAppConfiguration` 创建的配置可以通过 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 获取以用于后续操作，也可以通过 DI 作为服务获取。</span><span class="sxs-lookup"><span data-stu-id="3ab35-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="3ab35-194">主机配置也会添加到应用配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="3ab35-195">有关详细信息，请参阅 [ ASP.NET Core 中的配置](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="3ab35-196">适用于所有应用类型的设置</span><span class="sxs-lookup"><span data-stu-id="3ab35-196">Settings for all app types</span></span>

<span data-ttu-id="3ab35-197">本部分列出了适用于 HTTP 和非 HTTP 工作负荷的主机设置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="3ab35-198">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="3ab35-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="3ab35-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="3ab35-199">ApplicationName</span></span>

<span data-ttu-id="3ab35-200">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="3ab35-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="3ab35-201">键：`applicationName` </span><span class="sxs-lookup"><span data-stu-id="3ab35-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="3ab35-202">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-202">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-203">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="3ab35-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="3ab35-204">**环境变量**：`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="3ab35-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="3ab35-205">要设置此值，请使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="3ab35-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="3ab35-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3ab35-206">ContentRootPath</span></span>

<span data-ttu-id="3ab35-207">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 属性决定主机从什么位置开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="3ab35-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="3ab35-208">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="3ab35-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="3ab35-209">键：`contentRoot` </span><span class="sxs-lookup"><span data-stu-id="3ab35-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="3ab35-210">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-210">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-211">**默认**：应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="3ab35-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="3ab35-212">**环境变量**：`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="3ab35-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="3ab35-213">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="3ab35-214">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="3ab35-214">For more information, see:</span></span>

* [<span data-ttu-id="3ab35-215">基础知识：内容根目录</span><span class="sxs-lookup"><span data-stu-id="3ab35-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="3ab35-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="3ab35-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="3ab35-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="3ab35-217">EnvironmentName</span></span>

<span data-ttu-id="3ab35-218">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 属性可以设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="3ab35-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="3ab35-219">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="3ab35-220">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3ab35-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="3ab35-221">键：`environment` </span><span class="sxs-lookup"><span data-stu-id="3ab35-221">**Key**: `environment`</span></span>  
<span data-ttu-id="3ab35-222">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-222">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-223">**默认**：`Production`</span><span class="sxs-lookup"><span data-stu-id="3ab35-223">**Default**: `Production`</span></span>  
<span data-ttu-id="3ab35-224">**环境变量**：`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="3ab35-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="3ab35-225">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="3ab35-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="3ab35-226">ShutdownTimeout</span></span>

<span data-ttu-id="3ab35-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时。</span><span class="sxs-lookup"><span data-stu-id="3ab35-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="3ab35-228">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="3ab35-228">The default value is five seconds.</span></span>  <span data-ttu-id="3ab35-229">在超时时间段中，主机：</span><span class="sxs-lookup"><span data-stu-id="3ab35-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="3ab35-230">触发 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="3ab35-231">尝试停止托管服务，对服务停止失败的错误进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="3ab35-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="3ab35-232">如果在所有托管服务停止之前就达到了超时时间，则会在应用关闭时会终止剩余的所有活动的服务。</span><span class="sxs-lookup"><span data-stu-id="3ab35-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="3ab35-233">即使没有完成处理工作，服务也会停止。</span><span class="sxs-lookup"><span data-stu-id="3ab35-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="3ab35-234">如果停止服务需要额外的时间，请增加超时时间。</span><span class="sxs-lookup"><span data-stu-id="3ab35-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="3ab35-235">键：`shutdownTimeoutSeconds` </span><span class="sxs-lookup"><span data-stu-id="3ab35-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="3ab35-236">类型：`int` </span><span class="sxs-lookup"><span data-stu-id="3ab35-236">**Type**: `int`</span></span>  
<span data-ttu-id="3ab35-237">**默认**：5 秒</span><span class="sxs-lookup"><span data-stu-id="3ab35-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="3ab35-238">**环境变量**：`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="3ab35-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="3ab35-239">若要设置此值，请使用环境变量或配置 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="3ab35-240">以下示例将超时设置为 20 秒：</span><span class="sxs-lookup"><span data-stu-id="3ab35-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="3ab35-241">禁用“在更改时重载应用配置”</span><span class="sxs-lookup"><span data-stu-id="3ab35-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="3ab35-242">[默认情况下](xref:fundamentals/configuration/index#default)，appsettings.json 和 appsettings.{Environment}.json 会在文件更改时重载   。</span><span class="sxs-lookup"><span data-stu-id="3ab35-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="3ab35-243">要在 ASP.NET Core 5.0 Preview 3 或更高版本中禁用此重载行为，请将 `hostBuilder:reloadConfigOnChange` 键设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="3ab35-244">键：`hostBuilder:reloadConfigOnChange` </span><span class="sxs-lookup"><span data-stu-id="3ab35-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="3ab35-245">类型：`bool`（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="3ab35-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3ab35-246">**默认**：`true`</span><span class="sxs-lookup"><span data-stu-id="3ab35-246">**Default**: `true`</span></span>  
<span data-ttu-id="3ab35-247">命令行参数：`hostBuilder:reloadConfigOnChange` </span><span class="sxs-lookup"><span data-stu-id="3ab35-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="3ab35-248">**环境变量**：`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="3ab35-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="3ab35-249">所有平台上的环境变量分层键都不支持冒号 (`:`) 分隔符。</span><span class="sxs-lookup"><span data-stu-id="3ab35-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="3ab35-250">有关详细信息，请参阅[环境变量](xref:fundamentals/configuration/index#environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="3ab35-251">适用于 Web 应用的设置</span><span class="sxs-lookup"><span data-stu-id="3ab35-251">Settings for web apps</span></span>

<span data-ttu-id="3ab35-252">一些主机设置仅适用于 HTTP 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="3ab35-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="3ab35-253">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="3ab35-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="3ab35-254">`IWebHostBuilder` 上的扩展方法适用于这些设置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="3ab35-255">显示如何调用扩展方法的示例代码假定 `webBuilder` 是 `IWebHostBuilder` 的实例，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="3ab35-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="3ab35-256">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="3ab35-256">CaptureStartupErrors</span></span>

<span data-ttu-id="3ab35-257">当 `false` 时，启动期间出错导致主机退出。</span><span class="sxs-lookup"><span data-stu-id="3ab35-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="3ab35-258">当 `true` 时，主机在启动期间捕获异常并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="3ab35-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="3ab35-259">键：`captureStartupErrors` </span><span class="sxs-lookup"><span data-stu-id="3ab35-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="3ab35-260">类型：`bool`（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="3ab35-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3ab35-261">**默认**：默认为 `false`，除非应用使用 Kestrel 在 IIS 后方运行，其中默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="3ab35-262">**环境变量**：`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="3ab35-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="3ab35-263">若要设置此值，使用配置或调用 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="3ab35-264">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="3ab35-264">DetailedErrors</span></span>

<span data-ttu-id="3ab35-265">如果启用，或环境为 `Development`，应用会捕获详细错误。</span><span class="sxs-lookup"><span data-stu-id="3ab35-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="3ab35-266">键：`detailedErrors` </span><span class="sxs-lookup"><span data-stu-id="3ab35-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="3ab35-267">类型：`bool`（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="3ab35-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3ab35-268">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="3ab35-268">**Default**: `false`</span></span>  
<span data-ttu-id="3ab35-269">**环境变量**：`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="3ab35-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="3ab35-270">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="3ab35-271">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="3ab35-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="3ab35-272">承载启动程序集的以分号分隔的字符串在启动时加载。</span><span class="sxs-lookup"><span data-stu-id="3ab35-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="3ab35-273">虽然配置值默认为空字符串，但是承载启动程序集会始终包含应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="3ab35-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="3ab35-274">提供承载启动程序集时，当应用在启动过程中生成其公用服务时将它们添加到应用的程序集加载。</span><span class="sxs-lookup"><span data-stu-id="3ab35-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="3ab35-275">键：`hostingStartupAssemblies` </span><span class="sxs-lookup"><span data-stu-id="3ab35-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="3ab35-276">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-276">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-277">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="3ab35-277">**Default**: Empty string</span></span>  
<span data-ttu-id="3ab35-278">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3ab35-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="3ab35-279">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="3ab35-280">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="3ab35-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="3ab35-281">承载启动程序集的以分号分隔的字符串在启动时排除。</span><span class="sxs-lookup"><span data-stu-id="3ab35-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="3ab35-282">键：`hostingStartupExcludeAssemblies` </span><span class="sxs-lookup"><span data-stu-id="3ab35-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="3ab35-283">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-283">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-284">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="3ab35-284">**Default**: Empty string</span></span>  
<span data-ttu-id="3ab35-285">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3ab35-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="3ab35-286">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="3ab35-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="3ab35-287">HTTPS_Port</span></span>

<span data-ttu-id="3ab35-288">HTTPS 重定向端口。</span><span class="sxs-lookup"><span data-stu-id="3ab35-288">The HTTPS redirect port.</span></span> <span data-ttu-id="3ab35-289">用于[强制实施 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3ab35-290">键：`https_port` </span><span class="sxs-lookup"><span data-stu-id="3ab35-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="3ab35-291">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-291">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-292">**默认**：未设置默认值。</span><span class="sxs-lookup"><span data-stu-id="3ab35-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="3ab35-293">**环境变量**：`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="3ab35-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="3ab35-294">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="3ab35-295">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="3ab35-295">PreferHostingUrls</span></span>

<span data-ttu-id="3ab35-296">指示主机是否应该侦听使用 `IWebHostBuilder` 配置的 URL，而不是使用 `IServer` 实现配置的 URL。</span><span class="sxs-lookup"><span data-stu-id="3ab35-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="3ab35-297">键：`preferHostingUrls` </span><span class="sxs-lookup"><span data-stu-id="3ab35-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="3ab35-298">类型：`bool`（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="3ab35-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3ab35-299">**默认**：`true`</span><span class="sxs-lookup"><span data-stu-id="3ab35-299">**Default**: `true`</span></span>  
<span data-ttu-id="3ab35-300">**环境变量**：`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="3ab35-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="3ab35-301">若要设置此值，请使用环境变量或调用 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="3ab35-302">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="3ab35-302">PreventHostingStartup</span></span>

<span data-ttu-id="3ab35-303">阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="3ab35-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="3ab35-304">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="3ab35-305">键：`preventHostingStartup` </span><span class="sxs-lookup"><span data-stu-id="3ab35-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="3ab35-306">类型：`bool`（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="3ab35-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3ab35-307">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="3ab35-307">**Default**: `false`</span></span>  
<span data-ttu-id="3ab35-308">**环境变量**：`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="3ab35-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="3ab35-309">若要设置此值，请使用环境变量或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="3ab35-310">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="3ab35-310">StartupAssembly</span></span>

<span data-ttu-id="3ab35-311">要搜索 `Startup` 类的程序集。</span><span class="sxs-lookup"><span data-stu-id="3ab35-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="3ab35-312">键：`startupAssembly` </span><span class="sxs-lookup"><span data-stu-id="3ab35-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="3ab35-313">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-313">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-314">**默认**：应用的程序集</span><span class="sxs-lookup"><span data-stu-id="3ab35-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="3ab35-315">**环境变量**：`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="3ab35-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="3ab35-316">若要设置此值，请使用环境变量或调用 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="3ab35-317">`UseStartup` 可以采用程序集名称 (`string`) 或类型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="3ab35-318">如果调用多个 `UseStartup` 方法，优先选择最后一个方法。</span><span class="sxs-lookup"><span data-stu-id="3ab35-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="3ab35-319">URL</span><span class="sxs-lookup"><span data-stu-id="3ab35-319">URLs</span></span>

<span data-ttu-id="3ab35-320">IP 地址或主机地址的分号分隔列表，其中包含服务器应针对请求侦听的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="3ab35-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="3ab35-321">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="3ab35-322">使用“\*”指示服务器应针对请求侦听的使用特定端口和协议（例如 `http://*:5000`）的 IP 地址或主机名。</span><span class="sxs-lookup"><span data-stu-id="3ab35-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="3ab35-323">协议（`http://` 或 `https://`）必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="3ab35-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="3ab35-324">不同的服务器支持的格式有所不同。</span><span class="sxs-lookup"><span data-stu-id="3ab35-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="3ab35-325">键：`urls` </span><span class="sxs-lookup"><span data-stu-id="3ab35-325">**Key**: `urls`</span></span>  
<span data-ttu-id="3ab35-326">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-326">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-327">**默认值**：`http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="3ab35-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="3ab35-328">**环境变量**：`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="3ab35-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="3ab35-329">若要设置此值，请使用环境变量或调用 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="3ab35-330">Kestrel 具有自己的终结点配置 API。</span><span class="sxs-lookup"><span data-stu-id="3ab35-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="3ab35-331">有关详细信息，请参阅 <xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="3ab35-332">WebRoot</span><span class="sxs-lookup"><span data-stu-id="3ab35-332">WebRoot</span></span>

<span data-ttu-id="3ab35-333">应用的静态资产的相对路径。</span><span class="sxs-lookup"><span data-stu-id="3ab35-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="3ab35-334">键：`webroot` </span><span class="sxs-lookup"><span data-stu-id="3ab35-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="3ab35-335">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-335">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-336">**默认**：默认值为 `wwwroot`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="3ab35-337">{content root}/wwwroot 的路径必须存在  。</span><span class="sxs-lookup"><span data-stu-id="3ab35-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="3ab35-338">如果该路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="3ab35-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="3ab35-339">**环境变量**：`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="3ab35-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="3ab35-340">若要设置此值，请使用环境变量或调用 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="3ab35-341">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="3ab35-341">For more information, see:</span></span>

* [<span data-ttu-id="3ab35-342">基础知识：Web 根目录</span><span class="sxs-lookup"><span data-stu-id="3ab35-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="3ab35-343">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3ab35-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="3ab35-344">管理主机生存期</span><span class="sxs-lookup"><span data-stu-id="3ab35-344">Manage the host lifetime</span></span>

<span data-ttu-id="3ab35-345">对生成的 <xref:Microsoft.Extensions.Hosting.IHost> 实现调用方法，以启动和停止应用。</span><span class="sxs-lookup"><span data-stu-id="3ab35-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="3ab35-346">这些方法会影响所有在服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="3ab35-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="3ab35-347">运行</span><span class="sxs-lookup"><span data-stu-id="3ab35-347">Run</span></span>

<span data-ttu-id="3ab35-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机。</span><span class="sxs-lookup"><span data-stu-id="3ab35-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="3ab35-349">RunAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-349">RunAsync</span></span>

<span data-ttu-id="3ab35-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="3ab35-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-351">RunConsoleAsync</span></span>

<span data-ttu-id="3ab35-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="3ab35-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="3ab35-353">Start</span><span class="sxs-lookup"><span data-stu-id="3ab35-353">Start</span></span>

<span data-ttu-id="3ab35-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="3ab35-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="3ab35-355">StartAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-355">StartAsync</span></span>

<span data-ttu-id="3ab35-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动主机并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="3ab35-357">在 `StartAsync` 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="3ab35-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="3ab35-358">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="3ab35-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="3ab35-359">StopAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-359">StopAsync</span></span>

<span data-ttu-id="3ab35-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="3ab35-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="3ab35-361">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="3ab35-361">WaitForShutdown</span></span>

<span data-ttu-id="3ab35-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 阻止调用线程，直到 IHostLifetime 触发关闭，例如通过 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM。</span><span class="sxs-lookup"><span data-stu-id="3ab35-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="3ab35-363">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="3ab35-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="3ab35-365">外部控件</span><span class="sxs-lookup"><span data-stu-id="3ab35-365">External control</span></span>

<span data-ttu-id="3ab35-366">使用可从外部调用的方法，能够实现对主机生存期的直接控制：</span><span class="sxs-lookup"><span data-stu-id="3ab35-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="3ab35-367">本文介绍了 .NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 并提供有关使用方法的指南。</span><span class="sxs-lookup"><span data-stu-id="3ab35-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="3ab35-368">什么是主机？</span><span class="sxs-lookup"><span data-stu-id="3ab35-368">What's a host?</span></span>

<span data-ttu-id="3ab35-369">主机是封装应用资源的对象，例如  ：</span><span class="sxs-lookup"><span data-stu-id="3ab35-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="3ab35-370">依赖关系注入 (DI)</span><span class="sxs-lookup"><span data-stu-id="3ab35-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="3ab35-371">Logging</span><span class="sxs-lookup"><span data-stu-id="3ab35-371">Logging</span></span>
* <span data-ttu-id="3ab35-372">Configuration</span><span class="sxs-lookup"><span data-stu-id="3ab35-372">Configuration</span></span>
* <span data-ttu-id="3ab35-373">`IHostedService` 实现</span><span class="sxs-lookup"><span data-stu-id="3ab35-373">`IHostedService` implementations</span></span>

<span data-ttu-id="3ab35-374">启动主机时，它对它在 DI 容器中找到的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的每个实现调用 `IHostedService.StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="3ab35-375">在 web 应用中，其中一个 `IHostedService` 实现是启动 [HTTP 服务器实现](xref:fundamentals/index#servers)的 web 服务。</span><span class="sxs-lookup"><span data-stu-id="3ab35-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="3ab35-376">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="3ab35-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="3ab35-377">在低于 3.0 的 ASP.NET Core 版本中，[Web 主机](xref:fundamentals/host/web-host)用于 HTTP 工作负载。</span><span class="sxs-lookup"><span data-stu-id="3ab35-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="3ab35-378">不再建议将 Web 主机用于 Web 应用，但该主机仍可仅用于后向兼容性。</span><span class="sxs-lookup"><span data-stu-id="3ab35-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="3ab35-379">设置主机</span><span class="sxs-lookup"><span data-stu-id="3ab35-379">Set up a host</span></span>

<span data-ttu-id="3ab35-380">主机通常由 `Program` 类中的代码配置、生成和运行。</span><span class="sxs-lookup"><span data-stu-id="3ab35-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="3ab35-381">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="3ab35-381">The `Main` method:</span></span>

* <span data-ttu-id="3ab35-382">调用 `CreateHostBuilder` 方法以创建和配置生成器对象。</span><span class="sxs-lookup"><span data-stu-id="3ab35-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="3ab35-383">对生成器对象调用 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ab35-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="3ab35-384">以下是用于非 HTTP 工作负载的 Program.cs  代码，其中单个 `IHostedService` 实现添加到 DI 容器。</span><span class="sxs-lookup"><span data-stu-id="3ab35-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="3ab35-385">对于 HTTP 工作负荷，`Main` 方法相同，但 `CreateHostBuilder` 调用 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="3ab35-386">如果应用使用 Entity Framework Core，不要更改 `CreateHostBuilder` 方法的名称或签名。</span><span class="sxs-lookup"><span data-stu-id="3ab35-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="3ab35-387">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)应查找一个无需运行应用即可配置主机的 `CreateHostBuilder` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ab35-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="3ab35-388">有关详细信息，请参阅[设计时 DbContext 创建](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="3ab35-389">默认生成器设置</span><span class="sxs-lookup"><span data-stu-id="3ab35-389">Default builder settings</span></span>

<span data-ttu-id="3ab35-390"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 方法：</span><span class="sxs-lookup"><span data-stu-id="3ab35-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="3ab35-391">将[内容根目录](xref:fundamentals/index#content-root)设置为由 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的路径。</span><span class="sxs-lookup"><span data-stu-id="3ab35-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="3ab35-392">通过以下项加载主机配置：</span><span class="sxs-lookup"><span data-stu-id="3ab35-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="3ab35-393">前缀为 `DOTNET_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="3ab35-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="3ab35-394">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3ab35-394">Command-line arguments.</span></span>
* <span data-ttu-id="3ab35-395">通过以下对象加载应用配置：</span><span class="sxs-lookup"><span data-stu-id="3ab35-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="3ab35-396">appsettings.json  。</span><span class="sxs-lookup"><span data-stu-id="3ab35-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="3ab35-397">appsettings.{Environment}.json  。</span><span class="sxs-lookup"><span data-stu-id="3ab35-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="3ab35-398">[密钥管理器](xref:security/app-secrets) 当应用在 `Development` 环境中运行时。</span><span class="sxs-lookup"><span data-stu-id="3ab35-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="3ab35-399">环境变量。</span><span class="sxs-lookup"><span data-stu-id="3ab35-399">Environment variables.</span></span>
  * <span data-ttu-id="3ab35-400">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3ab35-400">Command-line arguments.</span></span>
* <span data-ttu-id="3ab35-401">添加以下[日志记录](xref:fundamentals/logging/index)提供程序：</span><span class="sxs-lookup"><span data-stu-id="3ab35-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="3ab35-402">控制台</span><span class="sxs-lookup"><span data-stu-id="3ab35-402">Console</span></span>
  * <span data-ttu-id="3ab35-403">调试</span><span class="sxs-lookup"><span data-stu-id="3ab35-403">Debug</span></span>
  * <span data-ttu-id="3ab35-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="3ab35-404">EventSource</span></span>
  * <span data-ttu-id="3ab35-405">EventLog（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="3ab35-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="3ab35-406">当环境为“开发”时，启用[范围验证](xref:fundamentals/dependency-injection#scope-validation)和[依赖关系验证](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="3ab35-407">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="3ab35-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="3ab35-408">从前缀为 `ASPNETCORE_` 的环境变量加载主机配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="3ab35-409">使用应用的托管配置提供程序将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器设置为 web 服务器并对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="3ab35-410">有关 Kestrel 服务器默认选项，请参阅 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="3ab35-411">添加[主机筛选中间件](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="3ab35-412">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 等于 `true`，则添加[转接头中间件](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="3ab35-413">支持 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="3ab35-413">Enables IIS integration.</span></span> <span data-ttu-id="3ab35-414">有关 IIS 默认选项，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="3ab35-415">本文中后面的[所有应用类型的设置](#settings-for-all-app-types)和[ web 应用的设置](#settings-for-web-apps)部分介绍如何替代默认生成器设置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="3ab35-416">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="3ab35-416">Framework-provided services</span></span>

<span data-ttu-id="3ab35-417">自动注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="3ab35-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="3ab35-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3ab35-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="3ab35-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3ab35-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="3ab35-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3ab35-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="3ab35-421">有关框架提供的服务的详细信息，请参阅 <xref:fundamentals/dependency-injection#framework-provided-services>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="3ab35-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3ab35-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="3ab35-423">将 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>（以前称为 `IApplicationLifetime`）服务注入任何类以处理启动后和正常关闭任务。</span><span class="sxs-lookup"><span data-stu-id="3ab35-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="3ab35-424">接口上的三个属性是用于注册应用启动和应用停止事件处理程序方法的取消令牌。</span><span class="sxs-lookup"><span data-stu-id="3ab35-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="3ab35-425">该接口还包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="3ab35-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="3ab35-426">以下示例是注册 `IHostApplicationLifetime` 事件的 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="3ab35-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="3ab35-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="3ab35-427">IHostLifetime</span></span>

<span data-ttu-id="3ab35-428"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 实现控制主机何时启动和何时停止。</span><span class="sxs-lookup"><span data-stu-id="3ab35-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="3ab35-429">使用了已注册的最后一个实现。</span><span class="sxs-lookup"><span data-stu-id="3ab35-429">The last implementation registered is used.</span></span>

<span data-ttu-id="3ab35-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是默认的 `IHostLifetime` 实现。</span><span class="sxs-lookup"><span data-stu-id="3ab35-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="3ab35-431">`ConsoleLifetime`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="3ab35-432">侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="3ab35-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="3ab35-433">解除阻止 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="3ab35-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="3ab35-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3ab35-434">IHostEnvironment</span></span>

<span data-ttu-id="3ab35-435">将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 服务注册到一个类，获取关于以下设置的信息：</span><span class="sxs-lookup"><span data-stu-id="3ab35-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="3ab35-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="3ab35-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="3ab35-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="3ab35-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="3ab35-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3ab35-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="3ab35-439">Web 应用实现 `IWebHostEnvironment` 接口，该接口继承 `IHostEnvironment` 并添加 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="3ab35-440">主机配置</span><span class="sxs-lookup"><span data-stu-id="3ab35-440">Host configuration</span></span>

<span data-ttu-id="3ab35-441">主机配置用于 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 实现的属性。</span><span class="sxs-lookup"><span data-stu-id="3ab35-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="3ab35-442">主机配置可以从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) 获取。</span><span class="sxs-lookup"><span data-stu-id="3ab35-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="3ab35-443">在 `ConfigureAppConfiguration` 后，`HostBuilderContext.Configuration` 被替换为应用配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="3ab35-444">若要添加主机配置，请对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3ab35-445">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="3ab35-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3ab35-446">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="3ab35-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="3ab35-447">`CreateDefaultBuilder` 包含前缀为 `DOTNET_` 的环境变量提供程序和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3ab35-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3ab35-448">对于 web 应用程序，添加前缀为 `ASPNETCORE_` 的环境变量提供程序。</span><span class="sxs-lookup"><span data-stu-id="3ab35-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="3ab35-449">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="3ab35-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="3ab35-450">例如，`ASPNETCORE_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="3ab35-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="3ab35-451">以下示例创建主机配置：</span><span class="sxs-lookup"><span data-stu-id="3ab35-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="3ab35-452">应用配置</span><span class="sxs-lookup"><span data-stu-id="3ab35-452">App configuration</span></span>

<span data-ttu-id="3ab35-453">通过对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="3ab35-454">可多次调用 `ConfigureAppConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="3ab35-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="3ab35-455">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="3ab35-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="3ab35-456">由 `ConfigureAppConfiguration` 创建的配置可以通过 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 获取以用于后续操作，也可以通过 DI 作为服务获取。</span><span class="sxs-lookup"><span data-stu-id="3ab35-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="3ab35-457">主机配置也会添加到应用配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="3ab35-458">有关详细信息，请参阅 [ ASP.NET Core 中的配置](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="3ab35-459">适用于所有应用类型的设置</span><span class="sxs-lookup"><span data-stu-id="3ab35-459">Settings for all app types</span></span>

<span data-ttu-id="3ab35-460">本部分列出了适用于 HTTP 和非 HTTP 工作负荷的主机设置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="3ab35-461">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="3ab35-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="3ab35-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="3ab35-462">ApplicationName</span></span>

<span data-ttu-id="3ab35-463">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="3ab35-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="3ab35-464">键：`applicationName` </span><span class="sxs-lookup"><span data-stu-id="3ab35-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="3ab35-465">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-465">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-466">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="3ab35-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="3ab35-467">**环境变量**：`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="3ab35-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="3ab35-468">要设置此值，请使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="3ab35-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="3ab35-469">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3ab35-469">ContentRootPath</span></span>

<span data-ttu-id="3ab35-470">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 属性决定主机从什么位置开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="3ab35-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="3ab35-471">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="3ab35-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="3ab35-472">键：`contentRoot` </span><span class="sxs-lookup"><span data-stu-id="3ab35-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="3ab35-473">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-473">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-474">**默认**：应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="3ab35-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="3ab35-475">**环境变量**：`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="3ab35-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="3ab35-476">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="3ab35-477">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="3ab35-477">For more information, see:</span></span>

* [<span data-ttu-id="3ab35-478">基础知识：内容根目录</span><span class="sxs-lookup"><span data-stu-id="3ab35-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="3ab35-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="3ab35-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="3ab35-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="3ab35-480">EnvironmentName</span></span>

<span data-ttu-id="3ab35-481">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 属性可以设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="3ab35-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="3ab35-482">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="3ab35-483">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3ab35-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="3ab35-484">键：`environment` </span><span class="sxs-lookup"><span data-stu-id="3ab35-484">**Key**: `environment`</span></span>  
<span data-ttu-id="3ab35-485">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-485">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-486">**默认**：`Production`</span><span class="sxs-lookup"><span data-stu-id="3ab35-486">**Default**: `Production`</span></span>  
<span data-ttu-id="3ab35-487">**环境变量**：`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="3ab35-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="3ab35-488">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="3ab35-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="3ab35-489">ShutdownTimeout</span></span>

<span data-ttu-id="3ab35-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时。</span><span class="sxs-lookup"><span data-stu-id="3ab35-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="3ab35-491">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="3ab35-491">The default value is five seconds.</span></span>  <span data-ttu-id="3ab35-492">在超时时间段中，主机：</span><span class="sxs-lookup"><span data-stu-id="3ab35-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="3ab35-493">触发 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="3ab35-494">尝试停止托管服务，对服务停止失败的错误进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="3ab35-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="3ab35-495">如果在所有托管服务停止之前就达到了超时时间，则会在应用关闭时会终止剩余的所有活动的服务。</span><span class="sxs-lookup"><span data-stu-id="3ab35-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="3ab35-496">即使没有完成处理工作，服务也会停止。</span><span class="sxs-lookup"><span data-stu-id="3ab35-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="3ab35-497">如果停止服务需要额外的时间，请增加超时时间。</span><span class="sxs-lookup"><span data-stu-id="3ab35-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="3ab35-498">键：`shutdownTimeoutSeconds` </span><span class="sxs-lookup"><span data-stu-id="3ab35-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="3ab35-499">类型：`int` </span><span class="sxs-lookup"><span data-stu-id="3ab35-499">**Type**: `int`</span></span>  
<span data-ttu-id="3ab35-500">**默认**：5 秒</span><span class="sxs-lookup"><span data-stu-id="3ab35-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="3ab35-501">**环境变量**：`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="3ab35-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="3ab35-502">若要设置此值，请使用环境变量或配置 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="3ab35-503">以下示例将超时设置为 20 秒：</span><span class="sxs-lookup"><span data-stu-id="3ab35-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="3ab35-504">适用于 Web 应用的设置</span><span class="sxs-lookup"><span data-stu-id="3ab35-504">Settings for web apps</span></span>

<span data-ttu-id="3ab35-505">一些主机设置仅适用于 HTTP 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="3ab35-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="3ab35-506">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="3ab35-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="3ab35-507">`IWebHostBuilder` 上的扩展方法适用于这些设置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="3ab35-508">显示如何调用扩展方法的示例代码假定 `webBuilder` 是 `IWebHostBuilder` 的实例，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="3ab35-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="3ab35-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="3ab35-509">CaptureStartupErrors</span></span>

<span data-ttu-id="3ab35-510">当 `false` 时，启动期间出错导致主机退出。</span><span class="sxs-lookup"><span data-stu-id="3ab35-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="3ab35-511">当 `true` 时，主机在启动期间捕获异常并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="3ab35-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="3ab35-512">键：`captureStartupErrors` </span><span class="sxs-lookup"><span data-stu-id="3ab35-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="3ab35-513">类型：`bool`（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="3ab35-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3ab35-514">**默认**：默认为 `false`，除非应用使用 Kestrel 在 IIS 后方运行，其中默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="3ab35-515">**环境变量**：`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="3ab35-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="3ab35-516">若要设置此值，使用配置或调用 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="3ab35-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="3ab35-517">DetailedErrors</span></span>

<span data-ttu-id="3ab35-518">如果启用，或环境为 `Development`，应用会捕获详细错误。</span><span class="sxs-lookup"><span data-stu-id="3ab35-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="3ab35-519">键：`detailedErrors` </span><span class="sxs-lookup"><span data-stu-id="3ab35-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="3ab35-520">类型：`bool`（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="3ab35-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3ab35-521">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="3ab35-521">**Default**: `false`</span></span>  
<span data-ttu-id="3ab35-522">**环境变量**：`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="3ab35-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="3ab35-523">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="3ab35-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="3ab35-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="3ab35-525">承载启动程序集的以分号分隔的字符串在启动时加载。</span><span class="sxs-lookup"><span data-stu-id="3ab35-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="3ab35-526">虽然配置值默认为空字符串，但是承载启动程序集会始终包含应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="3ab35-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="3ab35-527">提供承载启动程序集时，当应用在启动过程中生成其公用服务时将它们添加到应用的程序集加载。</span><span class="sxs-lookup"><span data-stu-id="3ab35-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="3ab35-528">键：`hostingStartupAssemblies` </span><span class="sxs-lookup"><span data-stu-id="3ab35-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="3ab35-529">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-529">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-530">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="3ab35-530">**Default**: Empty string</span></span>  
<span data-ttu-id="3ab35-531">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3ab35-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="3ab35-532">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="3ab35-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="3ab35-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="3ab35-534">承载启动程序集的以分号分隔的字符串在启动时排除。</span><span class="sxs-lookup"><span data-stu-id="3ab35-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="3ab35-535">键：`hostingStartupExcludeAssemblies` </span><span class="sxs-lookup"><span data-stu-id="3ab35-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="3ab35-536">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-536">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-537">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="3ab35-537">**Default**: Empty string</span></span>  
<span data-ttu-id="3ab35-538">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3ab35-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="3ab35-539">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="3ab35-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="3ab35-540">HTTPS_Port</span></span>

<span data-ttu-id="3ab35-541">HTTPS 重定向端口。</span><span class="sxs-lookup"><span data-stu-id="3ab35-541">The HTTPS redirect port.</span></span> <span data-ttu-id="3ab35-542">用于[强制实施 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3ab35-543">键：`https_port` </span><span class="sxs-lookup"><span data-stu-id="3ab35-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="3ab35-544">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-544">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-545">**默认**：未设置默认值。</span><span class="sxs-lookup"><span data-stu-id="3ab35-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="3ab35-546">**环境变量**：`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="3ab35-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="3ab35-547">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="3ab35-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="3ab35-548">PreferHostingUrls</span></span>

<span data-ttu-id="3ab35-549">指示主机是否应该侦听使用 `IWebHostBuilder` 配置的 URL，而不是使用 `IServer` 实现配置的 URL。</span><span class="sxs-lookup"><span data-stu-id="3ab35-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="3ab35-550">键：`preferHostingUrls` </span><span class="sxs-lookup"><span data-stu-id="3ab35-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="3ab35-551">类型：`bool`（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="3ab35-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3ab35-552">**默认**：`true`</span><span class="sxs-lookup"><span data-stu-id="3ab35-552">**Default**: `true`</span></span>  
<span data-ttu-id="3ab35-553">**环境变量**：`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="3ab35-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="3ab35-554">若要设置此值，请使用环境变量或调用 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="3ab35-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="3ab35-555">PreventHostingStartup</span></span>

<span data-ttu-id="3ab35-556">阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="3ab35-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="3ab35-557">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="3ab35-558">键：`preventHostingStartup` </span><span class="sxs-lookup"><span data-stu-id="3ab35-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="3ab35-559">类型：`bool`（`true` 或 `1`） </span><span class="sxs-lookup"><span data-stu-id="3ab35-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="3ab35-560">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="3ab35-560">**Default**: `false`</span></span>  
<span data-ttu-id="3ab35-561">**环境变量**：`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="3ab35-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="3ab35-562">若要设置此值，请使用环境变量或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="3ab35-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="3ab35-563">StartupAssembly</span></span>

<span data-ttu-id="3ab35-564">要搜索 `Startup` 类的程序集。</span><span class="sxs-lookup"><span data-stu-id="3ab35-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="3ab35-565">键：`startupAssembly` </span><span class="sxs-lookup"><span data-stu-id="3ab35-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="3ab35-566">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-566">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-567">**默认**：应用的程序集</span><span class="sxs-lookup"><span data-stu-id="3ab35-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="3ab35-568">**环境变量**：`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="3ab35-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="3ab35-569">若要设置此值，请使用环境变量或调用 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="3ab35-570">`UseStartup` 可以采用程序集名称 (`string`) 或类型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="3ab35-571">如果调用多个 `UseStartup` 方法，优先选择最后一个方法。</span><span class="sxs-lookup"><span data-stu-id="3ab35-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="3ab35-572">URL</span><span class="sxs-lookup"><span data-stu-id="3ab35-572">URLs</span></span>

<span data-ttu-id="3ab35-573">IP 地址或主机地址的分号分隔列表，其中包含服务器应针对请求侦听的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="3ab35-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="3ab35-574">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="3ab35-575">使用“\*”指示服务器应针对请求侦听的使用特定端口和协议（例如 `http://*:5000`）的 IP 地址或主机名。</span><span class="sxs-lookup"><span data-stu-id="3ab35-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="3ab35-576">协议（`http://` 或 `https://`）必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="3ab35-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="3ab35-577">不同的服务器支持的格式有所不同。</span><span class="sxs-lookup"><span data-stu-id="3ab35-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="3ab35-578">键：`urls` </span><span class="sxs-lookup"><span data-stu-id="3ab35-578">**Key**: `urls`</span></span>  
<span data-ttu-id="3ab35-579">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-579">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-580">**默认值**：`http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="3ab35-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="3ab35-581">**环境变量**：`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="3ab35-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="3ab35-582">若要设置此值，请使用环境变量或调用 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="3ab35-583">Kestrel 具有自己的终结点配置 API。</span><span class="sxs-lookup"><span data-stu-id="3ab35-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="3ab35-584">有关详细信息，请参阅 <xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="3ab35-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="3ab35-585">WebRoot</span></span>

<span data-ttu-id="3ab35-586">应用的静态资产的相对路径。</span><span class="sxs-lookup"><span data-stu-id="3ab35-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="3ab35-587">键：`webroot` </span><span class="sxs-lookup"><span data-stu-id="3ab35-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="3ab35-588">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-588">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-589">**默认**：默认值为 `wwwroot`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="3ab35-590">{content root}/wwwroot 的路径必须存在  。</span><span class="sxs-lookup"><span data-stu-id="3ab35-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="3ab35-591">如果该路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="3ab35-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="3ab35-592">**环境变量**：`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="3ab35-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="3ab35-593">若要设置此值，请使用环境变量或调用 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="3ab35-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="3ab35-594">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="3ab35-594">For more information, see:</span></span>

* [<span data-ttu-id="3ab35-595">基础知识：Web 根目录</span><span class="sxs-lookup"><span data-stu-id="3ab35-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="3ab35-596">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="3ab35-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="3ab35-597">管理主机生存期</span><span class="sxs-lookup"><span data-stu-id="3ab35-597">Manage the host lifetime</span></span>

<span data-ttu-id="3ab35-598">对生成的 <xref:Microsoft.Extensions.Hosting.IHost> 实现调用方法，以启动和停止应用。</span><span class="sxs-lookup"><span data-stu-id="3ab35-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="3ab35-599">这些方法会影响所有在服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="3ab35-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="3ab35-600">运行</span><span class="sxs-lookup"><span data-stu-id="3ab35-600">Run</span></span>

<span data-ttu-id="3ab35-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机。</span><span class="sxs-lookup"><span data-stu-id="3ab35-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="3ab35-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-602">RunAsync</span></span>

<span data-ttu-id="3ab35-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="3ab35-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-604">RunConsoleAsync</span></span>

<span data-ttu-id="3ab35-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="3ab35-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="3ab35-606">Start</span><span class="sxs-lookup"><span data-stu-id="3ab35-606">Start</span></span>

<span data-ttu-id="3ab35-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="3ab35-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="3ab35-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-608">StartAsync</span></span>

<span data-ttu-id="3ab35-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动主机并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="3ab35-610">在 `StartAsync` 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="3ab35-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="3ab35-611">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="3ab35-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="3ab35-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-612">StopAsync</span></span>

<span data-ttu-id="3ab35-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="3ab35-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="3ab35-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="3ab35-614">WaitForShutdown</span></span>

<span data-ttu-id="3ab35-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 阻止调用线程，直到 IHostLifetime 触发关闭，例如通过 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM。</span><span class="sxs-lookup"><span data-stu-id="3ab35-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="3ab35-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="3ab35-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="3ab35-618">外部控件</span><span class="sxs-lookup"><span data-stu-id="3ab35-618">External control</span></span>

<span data-ttu-id="3ab35-619">使用可从外部调用的方法，能够实现对主机生存期的直接控制：</span><span class="sxs-lookup"><span data-stu-id="3ab35-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="3ab35-620">ASP.NET Core 应用配置和启动主机。</span><span class="sxs-lookup"><span data-stu-id="3ab35-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="3ab35-621">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="3ab35-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="3ab35-622">本文介绍 ASP.NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)，该主机用于无法处理 HTTP 请求的应用。</span><span class="sxs-lookup"><span data-stu-id="3ab35-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="3ab35-623">泛型主机的用途是将 HTTP 管道从 Web 主机 API 中分离出来，从而启用更多的主机方案。</span><span class="sxs-lookup"><span data-stu-id="3ab35-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="3ab35-624">基于泛型主机的消息、后台任务和其他非 HTTP 工作负载可从横切功能（如配置、依赖关系注入 [DI] 和日志记录）中受益。</span><span class="sxs-lookup"><span data-stu-id="3ab35-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="3ab35-625">泛型主机是 ASP.NET Core 2.1 中的新增功能，不适用于 Web 承载方案。</span><span class="sxs-lookup"><span data-stu-id="3ab35-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="3ab35-626">对于 Web 承载方案，请使用 [Web 主机](xref:fundamentals/host/web-host)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="3ab35-627">泛型主机将在未来版本中替换 Web 主机，并在 HTTP 和非 HTTP 方案中充当主要的主机 API。</span><span class="sxs-lookup"><span data-stu-id="3ab35-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="3ab35-628">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3ab35-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3ab35-629">在 [Visual Studio Code](https://code.visualstudio.com/) 中运行示例应用时，请使用外部或集成终端  。</span><span class="sxs-lookup"><span data-stu-id="3ab35-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="3ab35-630">请勿在 `internalConsole` 中运行示例。</span><span class="sxs-lookup"><span data-stu-id="3ab35-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="3ab35-631">在 Visual Studio Code 中设置控制台：</span><span class="sxs-lookup"><span data-stu-id="3ab35-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="3ab35-632">打开 .vscode/launch.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="3ab35-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="3ab35-633">在 .NET Core 启动（控制台）配置中，找到控制台条目   。</span><span class="sxs-lookup"><span data-stu-id="3ab35-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="3ab35-634">将值设置为 `externalTerminal` 或 `integratedTerminal`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="3ab35-635">介绍</span><span class="sxs-lookup"><span data-stu-id="3ab35-635">Introduction</span></span>

<span data-ttu-id="3ab35-636">通用主机库位于 <xref:Microsoft.Extensions.Hosting> 命名空间中，由 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="3ab35-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="3ab35-637">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)（ASP.NET Core 2.1 或更高版本）中包括 `Microsoft.Extensions.Hosting` 包。</span><span class="sxs-lookup"><span data-stu-id="3ab35-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="3ab35-638"><xref:Microsoft.Extensions.Hosting.IHostedService> 是执行代码的入口点。</span><span class="sxs-lookup"><span data-stu-id="3ab35-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="3ab35-639">每个 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现都按照 [ConfigureServices 中服务注册](#configureservices)的顺序执行。</span><span class="sxs-lookup"><span data-stu-id="3ab35-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="3ab35-640">主机启动时，每个 <xref:Microsoft.Extensions.Hosting.IHostedService> 上都会调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>，主机正常关闭时，以反向注册顺序调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="3ab35-641">设置主机</span><span class="sxs-lookup"><span data-stu-id="3ab35-641">Set up a host</span></span>

<span data-ttu-id="3ab35-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> 是供库和应用初始化、生成和运行主机的主要组件：</span><span class="sxs-lookup"><span data-stu-id="3ab35-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="3ab35-643">选项</span><span class="sxs-lookup"><span data-stu-id="3ab35-643">Options</span></span>

<span data-ttu-id="3ab35-644"><xref:Microsoft.Extensions.Hosting.HostOptions> 配置 <xref:Microsoft.Extensions.Hosting.IHost> 的选项。</span><span class="sxs-lookup"><span data-stu-id="3ab35-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="3ab35-645">关闭超时值</span><span class="sxs-lookup"><span data-stu-id="3ab35-645">Shutdown timeout</span></span>

<span data-ttu-id="3ab35-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时值。</span><span class="sxs-lookup"><span data-stu-id="3ab35-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="3ab35-647">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="3ab35-647">The default value is five seconds.</span></span>

<span data-ttu-id="3ab35-648">`Program.Main` 中的以下选项配置将默认值为 5 秒的关闭超时值增加至 20 秒：</span><span class="sxs-lookup"><span data-stu-id="3ab35-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="3ab35-649">默认服务</span><span class="sxs-lookup"><span data-stu-id="3ab35-649">Default services</span></span>

<span data-ttu-id="3ab35-650">在主机初始化期间注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="3ab35-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="3ab35-651">[环境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="3ab35-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="3ab35-652">[配置](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="3ab35-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="3ab35-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="3ab35-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="3ab35-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="3ab35-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="3ab35-655">[选项](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="3ab35-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="3ab35-656">[日志记录](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="3ab35-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="3ab35-657">主机配置</span><span class="sxs-lookup"><span data-stu-id="3ab35-657">Host configuration</span></span>

<span data-ttu-id="3ab35-658">主机配置的创建方式如下：</span><span class="sxs-lookup"><span data-stu-id="3ab35-658">Host configuration is created by:</span></span>

* <span data-ttu-id="3ab35-659">调用 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的扩展方法以设置[“内容根”](#content-root)和[“环境”](#environment)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="3ab35-660">从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中的配置提供程序读取配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="3ab35-661">扩展方法</span><span class="sxs-lookup"><span data-stu-id="3ab35-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="3ab35-662">应用程序键（名称）</span><span class="sxs-lookup"><span data-stu-id="3ab35-662">Application key (name)</span></span>

<span data-ttu-id="3ab35-663">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="3ab35-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="3ab35-664">要显式设置值，请使用 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)：</span><span class="sxs-lookup"><span data-stu-id="3ab35-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="3ab35-665">键：`applicationName` </span><span class="sxs-lookup"><span data-stu-id="3ab35-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="3ab35-666">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-666">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-667">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="3ab35-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="3ab35-668">**设置使用**：`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="3ab35-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="3ab35-669">**环境变量**：`<PREFIX_>APPLICATIONNAME`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="3ab35-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="3ab35-670">内容根</span><span class="sxs-lookup"><span data-stu-id="3ab35-670">Content root</span></span>

<span data-ttu-id="3ab35-671">此设置确定主机从哪里开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="3ab35-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="3ab35-672">键：`contentRoot` </span><span class="sxs-lookup"><span data-stu-id="3ab35-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="3ab35-673">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-673">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-674">**默认**：默认为应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="3ab35-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="3ab35-675">**设置使用**：`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="3ab35-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="3ab35-676">**环境变量**：`<PREFIX_>CONTENTROOT`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="3ab35-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="3ab35-677">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="3ab35-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="3ab35-678">有关详细信息，请参阅[基础知识：内容根目录](xref:fundamentals/index#content-root)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="3ab35-679">环境</span><span class="sxs-lookup"><span data-stu-id="3ab35-679">Environment</span></span>

<span data-ttu-id="3ab35-680">设置应用的[环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="3ab35-681">键：`environment` </span><span class="sxs-lookup"><span data-stu-id="3ab35-681">**Key**: `environment`</span></span>  
<span data-ttu-id="3ab35-682">类型：`string` </span><span class="sxs-lookup"><span data-stu-id="3ab35-682">**Type**: `string`</span></span>  
<span data-ttu-id="3ab35-683">**默认**：`Production`</span><span class="sxs-lookup"><span data-stu-id="3ab35-683">**Default**: `Production`</span></span>  
<span data-ttu-id="3ab35-684">**设置使用**：`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="3ab35-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="3ab35-685">**环境变量**：`<PREFIX_>ENVIRONMENT`（`<PREFIX_>` 是[用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="3ab35-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="3ab35-686">可将环境设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="3ab35-686">The environment can be set to any value.</span></span> <span data-ttu-id="3ab35-687">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="3ab35-688">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3ab35-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="3ab35-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="3ab35-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="3ab35-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 来为主机创建 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="3ab35-691">主机配置用于初始化 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>，以供在应用的构建过程中使用。</span><span class="sxs-lookup"><span data-stu-id="3ab35-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="3ab35-692">可多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="3ab35-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="3ab35-693">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="3ab35-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="3ab35-694">默认情况下不包括提供程序。</span><span class="sxs-lookup"><span data-stu-id="3ab35-694">No providers are included by default.</span></span> <span data-ttu-id="3ab35-695">必须在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中显式指定应用所需的任何配置提供程序，包括：</span><span class="sxs-lookup"><span data-stu-id="3ab35-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="3ab35-696">文件配置（例如，来自 hostsettings.json 文件）  。</span><span class="sxs-lookup"><span data-stu-id="3ab35-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="3ab35-697">环境变量配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-697">Environment variable configuration.</span></span>
* <span data-ttu-id="3ab35-698">命令行参数配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="3ab35-699">任何其他所需的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="3ab35-699">Any other required configuration providers.</span></span>

<span data-ttu-id="3ab35-700">通过使用 `SetBasePath` 指定应用的基本路径，然后调用其中一个[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)，可以启用主机的文件配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="3ab35-701">示例应用使用 JSON 文件 hostsettings.json，并调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 来使用文件的主机配置设置  。</span><span class="sxs-lookup"><span data-stu-id="3ab35-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="3ab35-702">要添加主机的[环境变量配置](xref:fundamentals/configuration/index#environment-variables-configuration-provider)，请在主机生成器上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="3ab35-703">`AddEnvironmentVariables` 接受用户定义的前缀（可选）。</span><span class="sxs-lookup"><span data-stu-id="3ab35-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="3ab35-704">示例应用使用前缀 `PREFIX_`。</span><span class="sxs-lookup"><span data-stu-id="3ab35-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="3ab35-705">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="3ab35-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="3ab35-706">配置示例应用的主机后，`PREFIX_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="3ab35-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="3ab35-707">在开发过程中，如果使用 [Visual Studio](https://visualstudio.microsoft.com) 或通过 `dotnet run` 运行应用，可能会在 Properties/launchSettings.json  文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="3ab35-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="3ab35-708">若在开发过程中使用 [Visual Studio Code](https://code.visualstudio.com/)，可能会在 .vscode/launch.json  文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="3ab35-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="3ab35-709">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3ab35-710">通过调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 可添加[命令行配置](xref:fundamentals/configuration/index#command-line-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="3ab35-711">最后添加命令行配置以允许命令行参数替代之前配置提供程序提供的配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="3ab35-712">hostsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="3ab35-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="3ab35-713">可以通过 [applicationName](#application-key-name) 和 [contentRoot](#content-root) 键提供其他配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="3ab35-714">示例 `HostBuilder` 配置使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="3ab35-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="3ab35-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="3ab35-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="3ab35-716">通过在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 实现上调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="3ab35-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 来为应用创建 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="3ab35-718">可多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="3ab35-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="3ab35-719">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="3ab35-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="3ab35-720">[HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 中提供 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建的配置，以供进行后续操作和在 <xref:Microsoft.Extensions.Hosting.IHost.Services*> 中使用。</span><span class="sxs-lookup"><span data-stu-id="3ab35-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="3ab35-721">应用配置会自动接收 [ConfigureHostConfiguration](#configurehostconfiguration) 提供的主机配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="3ab35-722">示例应用配置使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="3ab35-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="3ab35-723">appsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="3ab35-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="3ab35-724">appsettings.Development.json  ：</span><span class="sxs-lookup"><span data-stu-id="3ab35-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="3ab35-725">appsettings.Production.json  ：</span><span class="sxs-lookup"><span data-stu-id="3ab35-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="3ab35-726">要将设置文件移动到输出目录，请在项目文件中将设置文件指定为 [MSBuild 项目项](/visualstudio/msbuild/common-msbuild-project-items)。</span><span class="sxs-lookup"><span data-stu-id="3ab35-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="3ab35-727">示例应用移动具有以下 `<Content>` 项的 JSON 应用设置文件和 hostsettings.json  ：</span><span class="sxs-lookup"><span data-stu-id="3ab35-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="3ab35-728">配置扩展方法（如 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 和 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>）需要其他 NuGet 包（如 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) 和 [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)）。</span><span class="sxs-lookup"><span data-stu-id="3ab35-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="3ab35-729">除非应用使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，否则除了核心 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) 包之外，还必须将这些包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="3ab35-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="3ab35-730">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="3ab35-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="3ab35-731">ConfigureServices</span></span>

<span data-ttu-id="3ab35-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 将服务添加到应用的[依赖关系](xref:fundamentals/dependency-injection)注入容器。</span><span class="sxs-lookup"><span data-stu-id="3ab35-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3ab35-733">可多次调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="3ab35-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="3ab35-734">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="3ab35-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="3ab35-735">有关详细信息，请参阅 <xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="3ab35-736">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)使用 `AddHostedService` 扩展方法向应用添加生存期事件 `LifetimeEventsHostedService` 和定时后台任务 `TimedHostedService` 服务：</span><span class="sxs-lookup"><span data-stu-id="3ab35-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="3ab35-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="3ab35-737">ConfigureLogging</span></span>

<span data-ttu-id="3ab35-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 添加了一个委托来配置提供的 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="3ab35-739">可以利用相加结果多次调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="3ab35-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="3ab35-740">UseConsoleLifetime</span></span>

<span data-ttu-id="3ab35-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="3ab35-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="3ab35-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 解除阻止 [ RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="3ab35-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="3ab35-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 预注册为默认生存期实现。</span><span class="sxs-lookup"><span data-stu-id="3ab35-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="3ab35-744">使用注册的最后一个生存期。</span><span class="sxs-lookup"><span data-stu-id="3ab35-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="3ab35-745">容器配置</span><span class="sxs-lookup"><span data-stu-id="3ab35-745">Container configuration</span></span>

<span data-ttu-id="3ab35-746">为支持插入其他容器中，主机可以接受 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="3ab35-747">提供工厂不属于 DI 容器注册，而是用于创建具体 DI 容器的主机内部函数。</span><span class="sxs-lookup"><span data-stu-id="3ab35-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="3ab35-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) 重写用于创建应用的服务提供程序的默认工厂。</span><span class="sxs-lookup"><span data-stu-id="3ab35-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="3ab35-749"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 方法托管自定义容器配置。</span><span class="sxs-lookup"><span data-stu-id="3ab35-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="3ab35-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 提供在基础主机 API 的基础之上配置容器的强类型体验。</span><span class="sxs-lookup"><span data-stu-id="3ab35-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="3ab35-751">可以利用相加结果多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="3ab35-752">为应用创建服务容器：</span><span class="sxs-lookup"><span data-stu-id="3ab35-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="3ab35-753">提供服务容器工厂：</span><span class="sxs-lookup"><span data-stu-id="3ab35-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="3ab35-754">使用该工厂并为应用配置自定义服务容器：</span><span class="sxs-lookup"><span data-stu-id="3ab35-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="3ab35-755">扩展性</span><span class="sxs-lookup"><span data-stu-id="3ab35-755">Extensibility</span></span>

<span data-ttu-id="3ab35-756">在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上使用扩展方法实现主机扩展性。</span><span class="sxs-lookup"><span data-stu-id="3ab35-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="3ab35-757">以下示例介绍扩展方法如何使用 <xref:fundamentals/host/hosted-services> 中所示的 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 示例来扩展 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 实现。</span><span class="sxs-lookup"><span data-stu-id="3ab35-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="3ab35-758">应用建立 `UseHostedService` 扩展方法，以注册在 `T` 中传递的托管服务：</span><span class="sxs-lookup"><span data-stu-id="3ab35-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="3ab35-759">管理主机</span><span class="sxs-lookup"><span data-stu-id="3ab35-759">Manage the host</span></span>

<span data-ttu-id="3ab35-760"><xref:Microsoft.Extensions.Hosting.IHost> 实现负责启动和停止服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="3ab35-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="3ab35-761">运行</span><span class="sxs-lookup"><span data-stu-id="3ab35-761">Run</span></span>

<span data-ttu-id="3ab35-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机：</span><span class="sxs-lookup"><span data-stu-id="3ab35-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="3ab35-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-763">RunAsync</span></span>

<span data-ttu-id="3ab35-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="3ab35-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="3ab35-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-765">RunConsoleAsync</span></span>

<span data-ttu-id="3ab35-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="3ab35-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="3ab35-767">Start 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-767">Start and StopAsync</span></span>

<span data-ttu-id="3ab35-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="3ab35-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="3ab35-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="3ab35-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="3ab35-770">StartAsync 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="3ab35-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动应用。</span><span class="sxs-lookup"><span data-stu-id="3ab35-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="3ab35-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 停止应用。</span><span class="sxs-lookup"><span data-stu-id="3ab35-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="3ab35-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="3ab35-773">WaitForShutdown</span></span>

<span data-ttu-id="3ab35-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 通过 <xref:Microsoft.Extensions.Hosting.IHostLifetime> 触发，例如 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`（侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="3ab35-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="3ab35-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="3ab35-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="3ab35-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="3ab35-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="3ab35-778">外部控件</span><span class="sxs-lookup"><span data-stu-id="3ab35-778">External control</span></span>

<span data-ttu-id="3ab35-779">使用可从外部调用的方法，能够实现主机的外部控件：</span><span class="sxs-lookup"><span data-stu-id="3ab35-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="3ab35-780">在 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="3ab35-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="3ab35-781">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="3ab35-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="3ab35-782">IHostingEnvironment 接口</span><span class="sxs-lookup"><span data-stu-id="3ab35-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="3ab35-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 提供有关应用托管环境的信息。</span><span class="sxs-lookup"><span data-stu-id="3ab35-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="3ab35-784">使用[构造函数注入](xref:fundamentals/dependency-injection)获取 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 以使用其属性和扩展方法：</span><span class="sxs-lookup"><span data-stu-id="3ab35-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="3ab35-785">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="3ab35-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="3ab35-786">IApplicationLifetime 接口</span><span class="sxs-lookup"><span data-stu-id="3ab35-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="3ab35-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 允许启动后和关闭活动，包括正常关闭请求。</span><span class="sxs-lookup"><span data-stu-id="3ab35-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="3ab35-788">接口上的三个属性是用于注册 <xref:System.Action> 方法（用于定义启动和关闭事件）的取消标记。</span><span class="sxs-lookup"><span data-stu-id="3ab35-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="3ab35-789">取消标记</span><span class="sxs-lookup"><span data-stu-id="3ab35-789">Cancellation Token</span></span> | <span data-ttu-id="3ab35-790">触发条件</span><span class="sxs-lookup"><span data-stu-id="3ab35-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="3ab35-791">主机已完全启动。</span><span class="sxs-lookup"><span data-stu-id="3ab35-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="3ab35-792">主机正在完成正常关闭。</span><span class="sxs-lookup"><span data-stu-id="3ab35-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="3ab35-793">应处理所有请求。</span><span class="sxs-lookup"><span data-stu-id="3ab35-793">All requests should be processed.</span></span> <span data-ttu-id="3ab35-794">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="3ab35-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="3ab35-795">主机正在执行正常关闭。</span><span class="sxs-lookup"><span data-stu-id="3ab35-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="3ab35-796">仍在处理请求。</span><span class="sxs-lookup"><span data-stu-id="3ab35-796">Requests may still be processing.</span></span> <span data-ttu-id="3ab35-797">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="3ab35-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="3ab35-798">构造函数将 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 服务注入到任何类中。</span><span class="sxs-lookup"><span data-stu-id="3ab35-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="3ab35-799">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)将构造函数注入到 `LifetimeEventsHostedService` 类（一个 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现）中，用于注册事件。</span><span class="sxs-lookup"><span data-stu-id="3ab35-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="3ab35-800">LifetimeEventsHostedService.cs  ：</span><span class="sxs-lookup"><span data-stu-id="3ab35-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="3ab35-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 请求终止应用。</span><span class="sxs-lookup"><span data-stu-id="3ab35-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="3ab35-802">以下类在调用类的 `Shutdown` 方法时使用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 正常关闭应用：</span><span class="sxs-lookup"><span data-stu-id="3ab35-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3ab35-803">其他资源</span><span class="sxs-lookup"><span data-stu-id="3ab35-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
