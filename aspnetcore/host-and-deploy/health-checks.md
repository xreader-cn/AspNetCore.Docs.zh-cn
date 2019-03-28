---
title: ASP.NET Core 中的运行状况检查
author: guardrex
description: 了解如何为 ASP.NET Core 基础结构（如应用和数据库）设置运行状况检查。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 0bb80a5fccc8240c6f1fb8e59b379766bfd90d9e
ms.sourcegitcommit: 687ffb15ebe65379f75c84739ea851d5a0d788b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488710"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="d3e62-103">ASP.NET Core 中的运行状况检查</span><span class="sxs-lookup"><span data-stu-id="d3e62-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="d3e62-104">通过 [Luke Latham](https://github.com/guardrex) 和 [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="d3e62-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

<span data-ttu-id="d3e62-105">ASP.NET Core 提供运行状况检查中间件和库，以用于报告应用基础结构组件的运行状况。</span><span class="sxs-lookup"><span data-stu-id="d3e62-105">ASP.NET Core offers Health Check Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="d3e62-106">运行状况检查由应用程序作为 HTTP 终结点公开。</span><span class="sxs-lookup"><span data-stu-id="d3e62-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="d3e62-107">可以为各种实时监视方案配置运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="d3e62-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="d3e62-108">运行状况探测可以由容器业务流程协调程和负载均衡器用于检查应用的状态。</span><span class="sxs-lookup"><span data-stu-id="d3e62-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="d3e62-109">例如，容器业务流程协调程序可以通过停止滚动部署或重新启动容器来响应失败的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="d3e62-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="d3e62-110">负载均衡器可以通过将流量从失败的实例路由到正常实例，来应对不正常的应用。</span><span class="sxs-lookup"><span data-stu-id="d3e62-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="d3e62-111">可以监视内存、磁盘和其他物理服务器资源的使用情况来了解是否处于正常状态。</span><span class="sxs-lookup"><span data-stu-id="d3e62-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="d3e62-112">运行状况检查可以测试应用的依赖项（如数据库和外部服务终结点）以确认是否可用和正常工作。</span><span class="sxs-lookup"><span data-stu-id="d3e62-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="d3e62-113">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d3e62-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d3e62-114">示例应用包含本主题中所述的方案示例。</span><span class="sxs-lookup"><span data-stu-id="d3e62-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="d3e62-115">若要运行给定方案的示例应用，请在命令行界面中从项目文件夹中使用 [dotnet run](/dotnet/core/tools/dotnet-run) 命令。</span><span class="sxs-lookup"><span data-stu-id="d3e62-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="d3e62-116">请参阅示例应用的 README.md 文件和本主题中的方案说明，以了解有关如何使用示例应用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="d3e62-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d3e62-117">系统必备</span><span class="sxs-lookup"><span data-stu-id="d3e62-117">Prerequisites</span></span>

<span data-ttu-id="d3e62-118">运行状况检查通常与外部监视服务或容器业务流程协调程序一起用于检查应用的状态。</span><span class="sxs-lookup"><span data-stu-id="d3e62-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="d3e62-119">向应用添加运行状况检查之前，需确定要使用的监视系统。</span><span class="sxs-lookup"><span data-stu-id="d3e62-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="d3e62-120">监视系统决定了要创建的运行状况检查类型以及配置其终结点的方式。</span><span class="sxs-lookup"><span data-stu-id="d3e62-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="d3e62-121">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 包。</span><span class="sxs-lookup"><span data-stu-id="d3e62-121">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="d3e62-122">示例应用提供了启动代码来演示几个方案的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="d3e62-122">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="d3e62-123">[数据库探测](#database-probe)方案使用 [BeatPulse](https://github.com/Xabaril/BeatPulse) 检查数据库连接的运行状况。</span><span class="sxs-lookup"><span data-stu-id="d3e62-123">The [database probe](#database-probe) scenario checks the health of a database connection using [BeatPulse](https://github.com/Xabaril/BeatPulse).</span></span> <span data-ttu-id="d3e62-124">[DbContext 探测](#entity-framework-core-dbcontext-probe)方案使用 EF Core `DbContext` 检查数据库。</span><span class="sxs-lookup"><span data-stu-id="d3e62-124">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="d3e62-125">若要探索数据库方案，示例应用将：</span><span class="sxs-lookup"><span data-stu-id="d3e62-125">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="d3e62-126">创建一个数据库，并在 appsettings.json 文件中提供其连接字符串。</span><span class="sxs-lookup"><span data-stu-id="d3e62-126">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="d3e62-127">其项目文件中具有以下包引用：</span><span class="sxs-lookup"><span data-stu-id="d3e62-127">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="d3e62-128">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="d3e62-128">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="d3e62-129">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d3e62-129">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="d3e62-130">[BeatPulse](https://github.com/Xabaril/BeatPulse) 不由 Microsoft 进行支持或维护。</span><span class="sxs-lookup"><span data-stu-id="d3e62-130">[BeatPulse](https://github.com/Xabaril/BeatPulse) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="d3e62-131">另一个运行状况检查方案演示如何将运行状况检查筛选到某个管理端口。</span><span class="sxs-lookup"><span data-stu-id="d3e62-131">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="d3e62-132">示例应用要求创建包含管理 URL 和管理端口的 Properties/launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="d3e62-132">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="d3e62-133">有关详细信息，请参阅[按端口筛选](#filter-by-port)部分。</span><span class="sxs-lookup"><span data-stu-id="d3e62-133">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="d3e62-134">基本运行状况探测</span><span class="sxs-lookup"><span data-stu-id="d3e62-134">Basic health probe</span></span>

<span data-ttu-id="d3e62-135">对于许多应用，报告应用在处理请求方面的可用性（运行情况）的基本运行状况探测配置足以发现应用的状态。</span><span class="sxs-lookup"><span data-stu-id="d3e62-135">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="d3e62-136">基本配置会注册运行状况检查服务，并调用运行状况检查中间件以使用运行状况响应在 URL 终结点处进行响应。</span><span class="sxs-lookup"><span data-stu-id="d3e62-136">The basic configuration registers health check services and calls the Health Check Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="d3e62-137">默认情况下，不会注册任何特定运行状况检查来测试任何特定依赖项或子系统。</span><span class="sxs-lookup"><span data-stu-id="d3e62-137">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="d3e62-138">如果能够在运行状况终结点 URL 处进行响应，则应用被视为正常。</span><span class="sxs-lookup"><span data-stu-id="d3e62-138">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="d3e62-139">默认响应编写器会以纯文本响应形式将状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) 写回到客户端，以便指示 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 或 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 状态。</span><span class="sxs-lookup"><span data-stu-id="d3e62-139">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="d3e62-140">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="d3e62-140">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d3e62-141">在 `Startup.Configure` 的请求处理管道中使用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 注册运行状况检查中间件。</span><span class="sxs-lookup"><span data-stu-id="d3e62-141">Add Health Check Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="d3e62-142">在示例应用中，在 `/health` 处创建运行状况检查终结点 (BasicStartup.cs)：</span><span class="sxs-lookup"><span data-stu-id="d3e62-142">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/BasicStartup.cs?name=snippet1&highlight=5,10)]

<span data-ttu-id="d3e62-143">若要使用示例应用运行基本配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d3e62-143">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="d3e62-144">Docker 示例</span><span class="sxs-lookup"><span data-stu-id="d3e62-144">Docker example</span></span>

<span data-ttu-id="d3e62-145">[Docker](xref:host-and-deploy/docker/index) 提供内置 `HEALTHCHECK` 指令，该指令可以用于检查使用基本运行状况检查配置的应用的状态：</span><span class="sxs-lookup"><span data-stu-id="d3e62-145">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="d3e62-146">创建运行状况检查</span><span class="sxs-lookup"><span data-stu-id="d3e62-146">Create health checks</span></span>

<span data-ttu-id="d3e62-147">运行状况检查通过实现 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 接口进行创建。</span><span class="sxs-lookup"><span data-stu-id="d3e62-147">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="d3e62-148"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 方法会返回 `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> `>`，它以 `Healthy`、`Degraded` 或 `Unhealthy` 的形式指示运行状况。</span><span class="sxs-lookup"><span data-stu-id="d3e62-148">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> `>` that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="d3e62-149">结果会使用可配置状态代码（配置在[运行状况检查选项](#health-check-options)部分中进行介绍）编写为纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="d3e62-149">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="d3e62-150"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 还可以返回可选的键值对。</span><span class="sxs-lookup"><span data-stu-id="d3e62-150"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="d3e62-151">示例运行状况检查</span><span class="sxs-lookup"><span data-stu-id="d3e62-151">Example health check</span></span>

<span data-ttu-id="d3e62-152">下面的 `ExampleHealthCheck` 类演示运行状况检查的布局：</span><span class="sxs-lookup"><span data-stu-id="d3e62-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check:</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public ExampleHealthCheck()
    {
        // Use dependency injection (DI) to supply any required services to the
        // health check.
    }

    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Execute health check logic here. This example sets a dummy
        // variable to true.
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="d3e62-153">注册运行状况检查服务</span><span class="sxs-lookup"><span data-stu-id="d3e62-153">Register health check services</span></span>

<span data-ttu-id="d3e62-154">`ExampleHealthCheck` 类型使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 添加到运行状况检查服务：</span><span class="sxs-lookup"><span data-stu-id="d3e62-154">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck<ExampleHealthCheck>("example_health_check");
}
```

<span data-ttu-id="d3e62-155">以下示例中显示的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 重载会设置要在运行状况检查报告失败时报告的失败状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-155">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="d3e62-156">如果失败状态设置为 `null`（默认值），则会报告 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-156">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="d3e62-157">此重载对于库创建者是一种十分有用的方案，在这种情况下，如果运行状况检查实现遵循该设置，则在发生运行状况检查失败时，应用会强制实施库所指示的失败状态。</span><span class="sxs-lookup"><span data-stu-id="d3e62-157">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="d3e62-158">标记用于筛选运行状况检查（在[筛选运行状况检查](#filter-health-checks)部分中进行了进一步介绍）。</span><span class="sxs-lookup"><span data-stu-id="d3e62-158">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="d3e62-159"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 还可以执行 lambda 函数。</span><span class="sxs-lookup"><span data-stu-id="d3e62-159"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="d3e62-160">在以下示例中，运行状况检查名称指定为 `Example`，并且检查始终返回正常状态：</span><span class="sxs-lookup"><span data-stu-id="d3e62-160">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Example", () =>
            HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" })
}
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="d3e62-161">使用运行状况检查中间件</span><span class="sxs-lookup"><span data-stu-id="d3e62-161">Use Health Checks Middleware</span></span>

<span data-ttu-id="d3e62-162">在 `Startup.Configure` 内，在处理管道中使用终结点 URL 或相对路径调用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>：</span><span class="sxs-lookup"><span data-stu-id="d3e62-162">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health");
}
```

<span data-ttu-id="d3e62-163">如果运行状况检查应侦听特定端口，则使用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 的重载设置端口（在[按端口筛选](#filter-by-port)部分中进行了进一步介绍）：</span><span class="sxs-lookup"><span data-stu-id="d3e62-163">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

<span data-ttu-id="d3e62-164">运行状况检查中间件是应用请求处理管道中的*终端中间件*。</span><span class="sxs-lookup"><span data-stu-id="d3e62-164">Health Checks Middleware is a *terminal middleware* in the app's request processing pipeline.</span></span> <span data-ttu-id="d3e62-165">遇到的与请求 URL 完全匹配的第一个运行状况检查终结点会进行执行，并且使中间件管道的其余部分短路。</span><span class="sxs-lookup"><span data-stu-id="d3e62-165">The first health check endpoint encountered that's an exact match to the request URL executes and short-circuits the rest of the middleware pipeline.</span></span> <span data-ttu-id="d3e62-166">当短路发生时，不会执行匹配运行状况检查后面的任何中间件。</span><span class="sxs-lookup"><span data-stu-id="d3e62-166">When short-circuiting occurs, no middleware following the matched health check executes.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="d3e62-167">运行状况检查选项</span><span class="sxs-lookup"><span data-stu-id="d3e62-167">Health check options</span></span>

<span data-ttu-id="d3e62-168"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> 使你可以自定义运行状况检查行为：</span><span class="sxs-lookup"><span data-stu-id="d3e62-168"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="d3e62-169">筛选运行状况检查</span><span class="sxs-lookup"><span data-stu-id="d3e62-169">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="d3e62-170">自定义 HTTP 状态代码</span><span class="sxs-lookup"><span data-stu-id="d3e62-170">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="d3e62-171">取消缓存标头</span><span class="sxs-lookup"><span data-stu-id="d3e62-171">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="d3e62-172">自定义输出</span><span class="sxs-lookup"><span data-stu-id="d3e62-172">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="d3e62-173">筛选运行状况检查</span><span class="sxs-lookup"><span data-stu-id="d3e62-173">Filter health checks</span></span>

<span data-ttu-id="d3e62-174">默认情况下，运行状况检查中间件会运行所有已注册的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="d3e62-174">By default, Health Check Middleware runs all registered health checks.</span></span> <span data-ttu-id="d3e62-175">若要运行运行状况检查的子集，请提供向 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 选项返回布尔值的函数。</span><span class="sxs-lookup"><span data-stu-id="d3e62-175">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="d3e62-176">在以下示例中，`Bar` 运行状况检查在函数条件语句 中由于其标记 (`bar_tag`) 而被筛选掉，在条件语句中，仅当运行状况检查的 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 属性与 `foo_tag` 或 `baz_tag` 匹配时才返回 `true`：</span><span class="sxs-lookup"><span data-stu-id="d3e62-176">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // Filter out the 'Bar' health check. Only Foo and Baz execute.
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="d3e62-177">自定义 HTTP 状态代码</span><span class="sxs-lookup"><span data-stu-id="d3e62-177">Customize the HTTP status code</span></span>

<span data-ttu-id="d3e62-178">使用 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> 可自定义运行状况状态到 HTTP 状态代码的映射。</span><span class="sxs-lookup"><span data-stu-id="d3e62-178">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="d3e62-179">以下 <xref:Microsoft.AspNetCore.Http.StatusCodes> 分配是中间件所使用的默认值。</span><span class="sxs-lookup"><span data-stu-id="d3e62-179">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="d3e62-180">更改状态代码值以满足要求。</span><span class="sxs-lookup"><span data-stu-id="d3e62-180">Change the status code values to meet your requirements.</span></span>

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // The following StatusCodes are the default assignments for
        // the HealthStatus properties.
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
}
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="d3e62-181">取消缓存标头</span><span class="sxs-lookup"><span data-stu-id="d3e62-181">Suppress cache headers</span></span>

<span data-ttu-id="d3e62-182"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> 控制运行状况检查中间件是否将 HTTP 标头添加到探测响应以防止响应缓存。</span><span class="sxs-lookup"><span data-stu-id="d3e62-182"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Check Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="d3e62-183">如果值为 `false`（默认值），则中间件会设置或替代 `Cache-Control`、`Expires` 和 `Pragma` 标头以防止响应缓存。</span><span class="sxs-lookup"><span data-stu-id="d3e62-183">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="d3e62-184">如果值为 `true`，则中间件不会修改响应的缓存标头。</span><span class="sxs-lookup"><span data-stu-id="d3e62-184">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // The default value is false.
        AllowCachingResponses = false
    });
}
```

### <a name="customize-output"></a><span data-ttu-id="d3e62-185">自定义输出</span><span class="sxs-lookup"><span data-stu-id="d3e62-185">Customize output</span></span>

<span data-ttu-id="d3e62-186"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 选项可获取或设置用于编写响应的委托。</span><span class="sxs-lookup"><span data-stu-id="d3e62-186">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="d3e62-187">默认委托会使用 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 字符串值编写最小的纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="d3e62-187">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // WriteResponse is a delegate used to write the response.
        ResponseWriter = WriteResponse
    });
}

private static Task WriteResponse(HttpContext httpContext,
    HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

## <a name="database-probe"></a><span data-ttu-id="d3e62-188">数据库探测</span><span class="sxs-lookup"><span data-stu-id="d3e62-188">Database probe</span></span>

<span data-ttu-id="d3e62-189">运行状况检查可以指定数据库查询作为布尔测试来运行，以指示数据库是否在正常响应。</span><span class="sxs-lookup"><span data-stu-id="d3e62-189">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="d3e62-190">示例应用使用 [BeatPulse](https://github.com/Xabaril/BeatPulse)（适用于 ASP.NET Core 应用的运行状况检查库）对 SQL Server 数据库执行运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="d3e62-190">The sample app uses [BeatPulse](https://github.com/Xabaril/BeatPulse), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="d3e62-191">BeatPulse 会对数据库执行 `SELECT 1` 查询以确认数据库连接是否处于正常状态。</span><span class="sxs-lookup"><span data-stu-id="d3e62-191">BeatPulse executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="d3e62-192">使用查询检查数据库连接时，请选择快速返回的查询。</span><span class="sxs-lookup"><span data-stu-id="d3e62-192">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="d3e62-193">查询方法会面临使数据库过载和降低其性能的风险。</span><span class="sxs-lookup"><span data-stu-id="d3e62-193">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="d3e62-194">在大多数情况下，无需运行测试查询。</span><span class="sxs-lookup"><span data-stu-id="d3e62-194">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="d3e62-195">只需建立成功的数据库连接便足矣。</span><span class="sxs-lookup"><span data-stu-id="d3e62-195">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="d3e62-196">如果发现需要运行查询，请选择简单的 SELECT 查询，如 `SELECT 1`。</span><span class="sxs-lookup"><span data-stu-id="d3e62-196">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="d3e62-197">若要使用 BeatPulse 库，请包含对 [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="d3e62-197">In order to use the BeatPulse library, include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="d3e62-198">在应用的 appsettings.json 文件中提供有效数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="d3e62-198">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="d3e62-199">应用使用名为 `HealthCheckSample` 的 SQL Server 数据库：</span><span class="sxs-lookup"><span data-stu-id="d3e62-199">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="d3e62-200">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="d3e62-200">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d3e62-201">示例应用使用数据库连接字符串 (DbHealthStartup.cs) 调用 BeatPulse 的 `AddSqlServer` 方法：</span><span class="sxs-lookup"><span data-stu-id="d3e62-201">The sample app calls BeatPulse's `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d3e62-202">在 `Startup.Configure` 内，在应用处理管道中调用运行状况检查中间件：</span><span class="sxs-lookup"><span data-stu-id="d3e62-202">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_Configure)]

<span data-ttu-id="d3e62-203">若要使用示例应用运行数据库探测方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d3e62-203">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="d3e62-204">[BeatPulse](https://github.com/Xabaril/BeatPulse) 不由 Microsoft 进行支持或维护。</span><span class="sxs-lookup"><span data-stu-id="d3e62-204">[BeatPulse](https://github.com/Xabaril/BeatPulse) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="d3e62-205">Entity Framework Core DbContext 探测</span><span class="sxs-lookup"><span data-stu-id="d3e62-205">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="d3e62-206">`DbContext` 检查确认应用可以与为 EF Core `DbContext` 配置的数据库通信。</span><span class="sxs-lookup"><span data-stu-id="d3e62-206">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="d3e62-207">满足以下条件的应用支持 `DbContext` 检查：</span><span class="sxs-lookup"><span data-stu-id="d3e62-207">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="d3e62-208">使用 [Entity Framework (EF) Core](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-208">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="d3e62-209">包括对 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="d3e62-209">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="d3e62-210">`AddDbContextCheck<TContext>` 为 `DbContext` 注册运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="d3e62-210">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="d3e62-211">`DbContext` 作为方法的 `TContext` 提供。</span><span class="sxs-lookup"><span data-stu-id="d3e62-211">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="d3e62-212">重载可用于配置失败状态、标记和自定义测试查询。</span><span class="sxs-lookup"><span data-stu-id="d3e62-212">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="d3e62-213">默认情况下：</span><span class="sxs-lookup"><span data-stu-id="d3e62-213">By default:</span></span>

* <span data-ttu-id="d3e62-214">`DbContextHealthCheck` 调用 EF Core 的 `CanConnectAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="d3e62-214">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="d3e62-215">可以自定义在使用 `AddDbContextCheck` 方法重载检查运行状况时运行的操作。</span><span class="sxs-lookup"><span data-stu-id="d3e62-215">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="d3e62-216">运行状况检查的名称是 `TContext` 类型的名称。</span><span class="sxs-lookup"><span data-stu-id="d3e62-216">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="d3e62-217">在示例应用中，`AppDbContext` 会提供给 `AddDbContextCheck`，并在 `Startup.ConfigureServices` 中注册为服务。</span><span class="sxs-lookup"><span data-stu-id="d3e62-217">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="d3e62-218">*DbContextHealthStartup.cs*：</span><span class="sxs-lookup"><span data-stu-id="d3e62-218">*DbContextHealthStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d3e62-219">在示例应用中，`UseHealthChecks` 在 `Startup.Configure` 中添加运行状况检查中间件。</span><span class="sxs-lookup"><span data-stu-id="d3e62-219">In the sample app, `UseHealthChecks` adds the Health Check Middleware in `Startup.Configure`.</span></span>

<span data-ttu-id="d3e62-220">*DbContextHealthStartup.cs*：</span><span class="sxs-lookup"><span data-stu-id="d3e62-220">*DbContextHealthStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_Configure)]

<span data-ttu-id="d3e62-221">若要使用示例应用运行 `DbContext` 探测方案，请确认连接字符串指定的数据库在 SQL Server 实例中不存在。</span><span class="sxs-lookup"><span data-stu-id="d3e62-221">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="d3e62-222">如果该数据库存在，请删除它。</span><span class="sxs-lookup"><span data-stu-id="d3e62-222">If the database exists, delete it.</span></span>

<span data-ttu-id="d3e62-223">在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d3e62-223">Execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario dbcontext
```

<span data-ttu-id="d3e62-224">在应用运行之后，在浏览器中对 `/health` 终结点发出请求，从而检查运行状况。</span><span class="sxs-lookup"><span data-stu-id="d3e62-224">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="d3e62-225">数据库和 `AppDbContext` 不存在，因此应用提供以下响应：</span><span class="sxs-lookup"><span data-stu-id="d3e62-225">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="d3e62-226">触发示例应用以创建数据库。</span><span class="sxs-lookup"><span data-stu-id="d3e62-226">Trigger the sample app to create the database.</span></span> <span data-ttu-id="d3e62-227">向 `/createdatabase` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="d3e62-227">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="d3e62-228">应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="d3e62-228">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d3e62-229">向 `/health` 终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="d3e62-229">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d3e62-230">数据库和上下文存在，因此应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="d3e62-230">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="d3e62-231">触发示例应用以删除数据库。</span><span class="sxs-lookup"><span data-stu-id="d3e62-231">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="d3e62-232">向 `/deletedatabase` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="d3e62-232">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="d3e62-233">应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="d3e62-233">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="d3e62-234">向 `/health` 终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="d3e62-234">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="d3e62-235">应用提供不正常的响应：</span><span class="sxs-lookup"><span data-stu-id="d3e62-235">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="d3e62-236">单独的就绪情况和运行情况探测</span><span class="sxs-lookup"><span data-stu-id="d3e62-236">Separate readiness and liveness probes</span></span>

<span data-ttu-id="d3e62-237">在某些托管方案中，会使用一对区分两种应用状态的运行状况检查：</span><span class="sxs-lookup"><span data-stu-id="d3e62-237">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="d3e62-238">应用正常运行，但尚未准备好接收请求。</span><span class="sxs-lookup"><span data-stu-id="d3e62-238">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="d3e62-239">此状态是应用的就绪情况。</span><span class="sxs-lookup"><span data-stu-id="d3e62-239">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="d3e62-240">应用正常运行并响应请求。</span><span class="sxs-lookup"><span data-stu-id="d3e62-240">The app is functioning and responding to requests.</span></span> <span data-ttu-id="d3e62-241">此状态是应用的运行情况。</span><span class="sxs-lookup"><span data-stu-id="d3e62-241">This state is the app's *liveness*.</span></span>

<span data-ttu-id="d3e62-242">就绪情况检查通常执行更广泛和耗时的检查集，以确定应用的所有子系统和资源是否都可用。</span><span class="sxs-lookup"><span data-stu-id="d3e62-242">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="d3e62-243">运行情况检查只是执行一个快速检查，以确定应用是否可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="d3e62-243">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="d3e62-244">应用通过其就绪情况检查之后，无需使用成本高昂的就绪情况检查集来进一步增加应用负荷 &mdash; 后续检查只需检查运行情况。</span><span class="sxs-lookup"><span data-stu-id="d3e62-244">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="d3e62-245">示例应用包含运行状况检查，以报告[托管服务](xref:fundamentals/host/hosted-services)中长时间运行的启动任务的完成。</span><span class="sxs-lookup"><span data-stu-id="d3e62-245">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="d3e62-246">`StartupHostedServiceHealthCheck` 公开了属性 `StartupTaskCompleted`，托管服务在其长时间运行的任务完成时可以将该属性设置为 `true` (StartupHostedServiceHealthCheck.cs)：</span><span class="sxs-lookup"><span data-stu-id="d3e62-246">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="d3e62-247">长时间运行的后台任务由[托管服务](xref:fundamentals/host/hosted-services) (Services/StartupHostedService) 启动。</span><span class="sxs-lookup"><span data-stu-id="d3e62-247">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="d3e62-248">在该任务结束时，`StartupHostedServiceHealthCheck.StartupTaskCompleted` 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="d3e62-248">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="d3e62-249">运行状况检查在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 与托管服务一起注册。</span><span class="sxs-lookup"><span data-stu-id="d3e62-249">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="d3e62-250">因为托管服务必须对运行状况检查设置该属性，所以运行状况检查也会在服务容器 (LivenessProbeStartup.cs) 中进行注册：</span><span class="sxs-lookup"><span data-stu-id="d3e62-250">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d3e62-251">在 `Startup.Configure` 内，在应用处理管道中调用运行状况检查中间件。</span><span class="sxs-lookup"><span data-stu-id="d3e62-251">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="d3e62-252">在示例应用中，在 `/health/ready` 处为就绪情况检查，并且在 `/health/live` 处为运行情况检查创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="d3e62-252">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="d3e62-253">就绪情况检查会将运行状况检查筛选到具有 `ready` 标记的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="d3e62-253">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="d3e62-254">运行情况检查通过在 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) 中返回 `false` 来筛选出 `StartupHostedServiceHealthCheck`（有关详细信息，请参阅[筛选运行状况检查](#filter-health-checks)）：</span><span class="sxs-lookup"><span data-stu-id="d3e62-254">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_Configure)]

<span data-ttu-id="d3e62-255">若要使用示例应用运行就绪情况/运行情况配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d3e62-255">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario liveness
```

<span data-ttu-id="d3e62-256">在浏览器中，访问 `/health/ready` 几次，直到过了 15 秒。</span><span class="sxs-lookup"><span data-stu-id="d3e62-256">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="d3e62-257">运行状况检查会在前 15 秒内报告“运行不正常”。</span><span class="sxs-lookup"><span data-stu-id="d3e62-257">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="d3e62-258">15 秒之后，终结点会报告“运行正常”，这反映托管服务完成了长时间运行的任务。</span><span class="sxs-lookup"><span data-stu-id="d3e62-258">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="d3e62-259">此示例还创建了一个运行第一个就绪检查的运行状况检查发布服务器（<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现），延迟时间为两秒。</span><span class="sxs-lookup"><span data-stu-id="d3e62-259">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="d3e62-260">有关详细信息，请参阅[运行状况检查发布服务器](#health-check-publisher)部分。</span><span class="sxs-lookup"><span data-stu-id="d3e62-260">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="d3e62-261">Kubernetes 示例</span><span class="sxs-lookup"><span data-stu-id="d3e62-261">Kubernetes example</span></span>

<span data-ttu-id="d3e62-262">在诸如 [Kubernetes](https://kubernetes.io/) 这类环境中，使用单独的就绪情况和运行情况检查会十分有用。</span><span class="sxs-lookup"><span data-stu-id="d3e62-262">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="d3e62-263">在 Kubernetes 中，应用可能需要在接受请求之前执行耗时的启动工作，如基础数据库可用性测试。</span><span class="sxs-lookup"><span data-stu-id="d3e62-263">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="d3e62-264">使用单独检查使业务流程协调程序可以区分应用是否正常运行但尚未准备就绪，或是应用程序是否未能启动。</span><span class="sxs-lookup"><span data-stu-id="d3e62-264">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="d3e62-265">有关 Kubernetes 中的就绪情况和运行情况探测的详细信息，请参阅 Kubernetes 文档中的[配置运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-265">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="d3e62-266">以下示例演示如何使用 Kubernetes 就绪情况探测配置：</span><span class="sxs-lookup"><span data-stu-id="d3e62-266">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="d3e62-267">具有自定义响应编写器的基于指标的探测</span><span class="sxs-lookup"><span data-stu-id="d3e62-267">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="d3e62-268">示例应用演示具有自定义响应编写器的内存运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="d3e62-268">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="d3e62-269">如果应用使用的内存多于给定内存阈值（在示例应用中为 1 GB），则 `MemoryHealthCheck` 报告降级状态。</span><span class="sxs-lookup"><span data-stu-id="d3e62-269">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="d3e62-270"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 包括应用的垃圾回收器 (GC) 信息 (MemoryHealthCheck.cs)：</span><span class="sxs-lookup"><span data-stu-id="d3e62-270">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="d3e62-271">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="d3e62-271">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d3e62-272">`MemoryHealthCheck` 注册为服务，而不是通过将运行状况检查传递到 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 来启用它。</span><span class="sxs-lookup"><span data-stu-id="d3e62-272">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="d3e62-273">所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 注册服务都可供运行状况检查服务和中间件使用。</span><span class="sxs-lookup"><span data-stu-id="d3e62-273">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="d3e62-274">建议将运行状况检查服务注册为单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="d3e62-274">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="d3e62-275">CustomWriterStartup.cs：</span><span class="sxs-lookup"><span data-stu-id="d3e62-275">*CustomWriterStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="d3e62-276">在 `Startup.Configure` 内，在应用处理管道中调用运行状况检查中间件。</span><span class="sxs-lookup"><span data-stu-id="d3e62-276">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="d3e62-277">一个 `WriteResponse` 委托提供给 `ResponseWriter` 属性，以在执行运行状况检查时输出自定义 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="d3e62-277">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_Configure&highlight=6)]

<span data-ttu-id="d3e62-278">`WriteResponse` 方法将 `CompositeHealthCheckResult` 格式化为 JSON 对象，并生成运行状况检查响应的 JSON 输出：</span><span class="sxs-lookup"><span data-stu-id="d3e62-278">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="d3e62-279">若要使用示例应用运行具有自定义响应编写器输出的基于指标的探测，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d3e62-279">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="d3e62-280">[BeatPulse](https://github.com/Xabaril/BeatPulse) 包括基于指标的运行状况检查方案（包括磁盘存储和最大值运行情况检查）。</span><span class="sxs-lookup"><span data-stu-id="d3e62-280">[BeatPulse](https://github.com/Xabaril/BeatPulse) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="d3e62-281">[BeatPulse](https://github.com/Xabaril/BeatPulse) 不由 Microsoft 进行支持或维护。</span><span class="sxs-lookup"><span data-stu-id="d3e62-281">[BeatPulse](https://github.com/Xabaril/BeatPulse) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="d3e62-282">按端口筛选</span><span class="sxs-lookup"><span data-stu-id="d3e62-282">Filter by port</span></span>

<span data-ttu-id="d3e62-283">使用端口调用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 会将运行状况检查请求限制到指定端口。</span><span class="sxs-lookup"><span data-stu-id="d3e62-283">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="d3e62-284">这通常用于在容器环境中公开用于监视服务的端口。</span><span class="sxs-lookup"><span data-stu-id="d3e62-284">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="d3e62-285">示例应用使用[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)配置端口。</span><span class="sxs-lookup"><span data-stu-id="d3e62-285">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="d3e62-286">端口在 launchSettings.json 文件设置，并通过环境变量传递到配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="d3e62-286">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="d3e62-287">还必须配置服务器以在管理端口上侦听请求。</span><span class="sxs-lookup"><span data-stu-id="d3e62-287">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="d3e62-288">若要使用示例应用演示管理端口配置，请在 Properties 文件夹中创建 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="d3e62-288">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="d3e62-289">以下 launchSettings.json 文件未包含在示例应用的项目文件中，必须手动创建。</span><span class="sxs-lookup"><span data-stu-id="d3e62-289">The following *launchSettings.json* file isn't included in the sample app's project files and must be created manually.</span></span>

<span data-ttu-id="d3e62-290">Properties/launchSettings.json：</span><span class="sxs-lookup"><span data-stu-id="d3e62-290">*Properties/launchSettings.json*:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="d3e62-291">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="d3e62-291">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d3e62-292"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 调用指定管理端口 (ManagementPortStartup.cs)：</span><span class="sxs-lookup"><span data-stu-id="d3e62-292">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=12,18)]

> [!NOTE]
> <span data-ttu-id="d3e62-293">可以通过在代码中显式设置 URL 和管理端口，来避免在示例应用中创建 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="d3e62-293">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="d3e62-294">在创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 的 Program.cs 中，添加 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> 调用并提供应用的正常响应终结点和管理端口终结点。</span><span class="sxs-lookup"><span data-stu-id="d3e62-294">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="d3e62-295">在调用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 的 ManagementPortStartup.cs 中，显式指定管理端口。</span><span class="sxs-lookup"><span data-stu-id="d3e62-295">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="d3e62-296">Program.cs:</span><span class="sxs-lookup"><span data-stu-id="d3e62-296">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="d3e62-297">ManagementPortStartup.cs：</span><span class="sxs-lookup"><span data-stu-id="d3e62-297">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="d3e62-298">若要使用示例应用运行管理端口配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="d3e62-298">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="d3e62-299">分发运行状况检查库</span><span class="sxs-lookup"><span data-stu-id="d3e62-299">Distribute a health check library</span></span>

<span data-ttu-id="d3e62-300">将运行状况检查作为库进行分发：</span><span class="sxs-lookup"><span data-stu-id="d3e62-300">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="d3e62-301">编写将 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 接口作为独立类来实现的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="d3e62-301">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="d3e62-302">该类可以依赖于[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)类型激活和[命名选项](xref:fundamentals/configuration/options)来访问配置数据。</span><span class="sxs-lookup"><span data-stu-id="d3e62-302">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   // Health check logic
                   //
                   // data1 and data2 are used in the method to
                   // run the probe's health check logic.

                   // Assume that it's possible for this health check
                   // to throw an AccessViolationException.

                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="d3e62-303">使用参数编写一个扩展方法，所使用的应用会在其 `Startup.Configure` 方法中调用它。</span><span class="sxs-lookup"><span data-stu-id="d3e62-303">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="d3e62-304">在以下示例中，假设以下运行状况检查方法签名：</span><span class="sxs-lookup"><span data-stu-id="d3e62-304">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="d3e62-305">前面的签名指示 `ExampleHealthCheck` 需要其他数据来处理运行状况检查探测逻辑。</span><span class="sxs-lookup"><span data-stu-id="d3e62-305">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="d3e62-306">当运行状况检查向扩展方法注册时，数据会提供给用于创建运行状况检查实例的委托。</span><span class="sxs-lookup"><span data-stu-id="d3e62-306">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="d3e62-307">在以下示例中，调用方会指定可选的：</span><span class="sxs-lookup"><span data-stu-id="d3e62-307">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="d3e62-308">运行状况检查名称 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-308">health check name (`name`).</span></span> <span data-ttu-id="d3e62-309">如果为 `null`，则使用 `example_health_check`。</span><span class="sxs-lookup"><span data-stu-id="d3e62-309">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="d3e62-310">运行状况检查的字符串数据点 (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-310">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="d3e62-311">运行状况检查的整数数据点 (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-311">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="d3e62-312">如果为 `null`，则使用 `1`。</span><span class="sxs-lookup"><span data-stu-id="d3e62-312">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="d3e62-313">失败状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-313">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="d3e62-314">默认值为 `null`。</span><span class="sxs-lookup"><span data-stu-id="d3e62-314">The default is `null`.</span></span> <span data-ttu-id="d3e62-315">如果为 `null`，则报告失败状态 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-315">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="d3e62-316">标记 (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-316">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string NAME = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? NAME,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="d3e62-317">运行状况检查发布服务器</span><span class="sxs-lookup"><span data-stu-id="d3e62-317">Health Check Publisher</span></span>

<span data-ttu-id="d3e62-318">当 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 添加到服务容器时，运行状况检查系统，会定期执行运行状况检查并使用结果调用 `PublishAsync`。</span><span class="sxs-lookup"><span data-stu-id="d3e62-318">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="d3e62-319">在期望每个进程定期调用监视系统以便确定运行状况的基于推送的运行状况监视系统方案中，这十分有用。</span><span class="sxs-lookup"><span data-stu-id="d3e62-319">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="d3e62-320"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 接口具有单个方法：</span><span class="sxs-lookup"><span data-stu-id="d3e62-320">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="d3e62-321">使用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> 可设置：</span><span class="sxs-lookup"><span data-stu-id="d3e62-321"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="d3e62-322"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; 在应用程序启动后且在应用程序执行 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例之前所应用的初始延迟。</span><span class="sxs-lookup"><span data-stu-id="d3e62-322"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d3e62-323">延迟在启动时应用一次，不适用于后续迭代。</span><span class="sxs-lookup"><span data-stu-id="d3e62-323">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="d3e62-324">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="d3e62-324">The default value is five seconds.</span></span>
* <span data-ttu-id="d3e62-325"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 执行的时间。</span><span class="sxs-lookup"><span data-stu-id="d3e62-325"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="d3e62-326">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d3e62-326">The default value is 30 seconds.</span></span>
* <span data-ttu-id="d3e62-327"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; 如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> 为 `null`（默认值），则运行状况检查发布服务器服务运行所有已注册的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="d3e62-327"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="d3e62-328">若要运行运行状况检查的子集，请提供用于筛选检查集的函数。</span><span class="sxs-lookup"><span data-stu-id="d3e62-328">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="d3e62-329">每个时间段都会评估谓词。</span><span class="sxs-lookup"><span data-stu-id="d3e62-329">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="d3e62-330"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; 执行所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例的运行状况检查的超时时间。</span><span class="sxs-lookup"><span data-stu-id="d3e62-330"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="d3e62-331">在不超时的情况下，使用 <xref:System.Threading.Timeout.InfiniteTimeSpan> 执行。</span><span class="sxs-lookup"><span data-stu-id="d3e62-331">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="d3e62-332">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="d3e62-332">The default value is 30 seconds.</span></span>

::: moniker range="= aspnetcore-2.2"

> [!WARNING]
> <span data-ttu-id="d3e62-333">在 ASP.NET Core 2.2 版本中，<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现不支持设置 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>，它设置 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> 的值。</span><span class="sxs-lookup"><span data-stu-id="d3e62-333">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="d3e62-334">此问题将在 ASP.NET Core 3.0 中得以修复。</span><span class="sxs-lookup"><span data-stu-id="d3e62-334">This issue will be fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="d3e62-335">有关详细信息，请参阅 [HealthCheckPublisherOptions.Period 设置 .Delay 值](https://github.com/aspnet/Extensions/issues/1041)。</span><span class="sxs-lookup"><span data-stu-id="d3e62-335">For more information, see [HealthCheckPublisherOptions.Period sets the value of .Delay](https://github.com/aspnet/Extensions/issues/1041).</span></span>

::: moniker-end

<span data-ttu-id="d3e62-336">在示例应用中，`ReadinessPublisher` 是 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现。</span><span class="sxs-lookup"><span data-stu-id="d3e62-336">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="d3e62-337">运行状况检查状态在 `Entries` 中记录并针对每次检查记录：</span><span class="sxs-lookup"><span data-stu-id="d3e62-337">The health check status is recorded in `Entries` and logged for each check:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=20,22-23)]

<span data-ttu-id="d3e62-338">在示例应用的 `LivenessProbeStartup` 示例中，`StartupHostedService` 就绪状态检查有两秒的启动延迟，并且每 30 秒运行一次检查。</span><span class="sxs-lookup"><span data-stu-id="d3e62-338">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="d3e62-339">为激活 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现，示例将 `ReadinessPublisher` 注册为[依存关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中的单一实例服务：</span><span class="sxs-lookup"><span data-stu-id="d3e62-339">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

::: moniker range="= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="d3e62-340">以下解决方法允许在已将一个或多个其他托管服务添加到应用时，向服务容器添加 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例。</span><span class="sxs-lookup"><span data-stu-id="d3e62-340">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="d3e62-341">ASP.NET Core 3.0 发布后，不再需要此解决方法。</span><span class="sxs-lookup"><span data-stu-id="d3e62-341">This workaround won't be required with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="d3e62-342">有关详细信息，请参阅： https://github.com/aspnet/Extensions/issues/639。</span><span class="sxs-lookup"><span data-stu-id="d3e62-342">For more information, see: https://github.com/aspnet/Extensions/issues/639.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="d3e62-343">[BeatPulse](https://github.com/Xabaril/BeatPulse) 包括多个系统的发布服务器（包括 [Application Insights](/azure/application-insights/app-insights-overview)）。</span><span class="sxs-lookup"><span data-stu-id="d3e62-343">[BeatPulse](https://github.com/Xabaril/BeatPulse) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="d3e62-344">[BeatPulse](https://github.com/Xabaril/BeatPulse) 不由 Microsoft 进行支持或维护。</span><span class="sxs-lookup"><span data-stu-id="d3e62-344">[BeatPulse](https://github.com/Xabaril/BeatPulse) isn't maintained or supported by Microsoft.</span></span>
