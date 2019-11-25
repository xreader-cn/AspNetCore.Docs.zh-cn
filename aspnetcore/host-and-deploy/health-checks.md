---
title: ASP.NET Core 中的运行状况检查
author: guardrex
description: 了解如何为 ASP.NET Core 基础结构（如应用和数据库）设置运行状况检查。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 11/13/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 4a4606a58178018f0d71d467d4c8b6c9982c09dc
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74115994"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="64f90-103">ASP.NET Core 中的运行状况检查</span><span class="sxs-lookup"><span data-stu-id="64f90-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="64f90-104">通过 [Luke Latham](https://github.com/guardrex) 和 [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="64f90-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="64f90-105">ASP.NET Core 提供运行状况检查中间件和库，以用于报告应用基础结构组件的运行状况。</span><span class="sxs-lookup"><span data-stu-id="64f90-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="64f90-106">运行状况检查由应用程序作为 HTTP 终结点公开。</span><span class="sxs-lookup"><span data-stu-id="64f90-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="64f90-107">可以为各种实时监视方案配置运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="64f90-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="64f90-108">运行状况探测可以由容器业务流程协调程和负载均衡器用于检查应用的状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="64f90-109">例如，容器业务流程协调程序可以通过停止滚动部署或重新启动容器来响应失败的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="64f90-110">负载均衡器可以通过将流量从失败的实例路由到正常实例，来应对不正常的应用。</span><span class="sxs-lookup"><span data-stu-id="64f90-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="64f90-111">可以监视内存、磁盘和其他物理服务器资源的使用情况来了解是否处于正常状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="64f90-112">运行状况检查可以测试应用的依赖项（如数据库和外部服务终结点）以确认是否可用和正常工作。</span><span class="sxs-lookup"><span data-stu-id="64f90-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="64f90-113">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="64f90-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="64f90-114">示例应用包含本主题中所述的方案示例。</span><span class="sxs-lookup"><span data-stu-id="64f90-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="64f90-115">若要运行给定方案的示例应用，请在命令行界面中从项目文件夹中使用 [dotnet run](/dotnet/core/tools/dotnet-run) 命令。</span><span class="sxs-lookup"><span data-stu-id="64f90-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="64f90-116">请参阅示例应用的 README.md  文件和本主题中的方案说明，以了解有关如何使用示例应用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="64f90-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="64f90-117">系统必备</span><span class="sxs-lookup"><span data-stu-id="64f90-117">Prerequisites</span></span>

<span data-ttu-id="64f90-118">运行状况检查通常与外部监视服务或容器业务流程协调程序一起用于检查应用的状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="64f90-119">向应用添加运行状况检查之前，需确定要使用的监视系统。</span><span class="sxs-lookup"><span data-stu-id="64f90-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="64f90-120">监视系统决定了要创建的运行状况检查类型以及配置其终结点的方式。</span><span class="sxs-lookup"><span data-stu-id="64f90-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="64f90-121">为 ASP.NET Core 应用隐式引用 [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 包。</span><span class="sxs-lookup"><span data-stu-id="64f90-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="64f90-122">若要使用 Entity Framework Core 执行运行状况检查，请将包引用添加到 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 包。</span><span class="sxs-lookup"><span data-stu-id="64f90-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="64f90-123">示例应用提供了启动代码来演示几个方案的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="64f90-124">[数据库探测](#database-probe)方案使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 检查数据库连接的运行状况。</span><span class="sxs-lookup"><span data-stu-id="64f90-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="64f90-125">[DbContext 探测](#entity-framework-core-dbcontext-probe)方案使用 EF Core `DbContext` 检查数据库。</span><span class="sxs-lookup"><span data-stu-id="64f90-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="64f90-126">若要探索数据库方案，示例应用将：</span><span class="sxs-lookup"><span data-stu-id="64f90-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="64f90-127">创建一个数据库，并在 appsettings.json 文件中提供其连接字符串  。</span><span class="sxs-lookup"><span data-stu-id="64f90-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="64f90-128">其项目文件中具有以下包引用：</span><span class="sxs-lookup"><span data-stu-id="64f90-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="64f90-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="64f90-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="64f90-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="64f90-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="64f90-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="64f90-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="64f90-132">另一个运行状况检查方案演示如何将运行状况检查筛选到某个管理端口。</span><span class="sxs-lookup"><span data-stu-id="64f90-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="64f90-133">示例应用要求创建包含管理 URL 和管理端口的 Properties/launchSettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="64f90-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="64f90-134">有关详细信息，请参阅[按端口筛选](#filter-by-port)部分。</span><span class="sxs-lookup"><span data-stu-id="64f90-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="64f90-135">基本运行状况探测</span><span class="sxs-lookup"><span data-stu-id="64f90-135">Basic health probe</span></span>

<span data-ttu-id="64f90-136">对于许多应用，报告应用在处理请求方面的可用性（运行情况  ）的基本运行状况探测配置足以发现应用的状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="64f90-137">基本配置会注册运行状况检查服务，并调用运行状况检查中间件以通过运行状况响应在 URL 终结点处进行响应。</span><span class="sxs-lookup"><span data-stu-id="64f90-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="64f90-138">默认情况下，不会注册任何特定运行状况检查来测试任何特定依赖项或子系统。</span><span class="sxs-lookup"><span data-stu-id="64f90-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="64f90-139">如果能够在运行状况终结点 URL 处进行响应，则应用被视为正常。</span><span class="sxs-lookup"><span data-stu-id="64f90-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="64f90-140">默认响应编写器会以纯文本响应形式将状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) 写回到客户端，以便指示 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 或 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="64f90-141">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="64f90-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="64f90-142">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="64f90-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="64f90-143">在示例应用中，在 `/health` 处创建运行状况检查终结点 (BasicStartup.cs  )：</span><span class="sxs-lookup"><span data-stu-id="64f90-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="64f90-144">若要使用示例应用运行基本配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="64f90-145">Docker 示例</span><span class="sxs-lookup"><span data-stu-id="64f90-145">Docker example</span></span>

<span data-ttu-id="64f90-146">[Docker](xref:host-and-deploy/docker/index) 提供内置 `HEALTHCHECK` 指令，该指令可以用于检查使用基本运行状况检查配置的应用的状态：</span><span class="sxs-lookup"><span data-stu-id="64f90-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="64f90-147">创建运行状况检查</span><span class="sxs-lookup"><span data-stu-id="64f90-147">Create health checks</span></span>

<span data-ttu-id="64f90-148">运行状况检查通过实现 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 接口进行创建。</span><span class="sxs-lookup"><span data-stu-id="64f90-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="64f90-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 方法会返回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，它以 `Healthy`、`Degraded` 或 `Unhealthy` 的形式指示运行状况。</span><span class="sxs-lookup"><span data-stu-id="64f90-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="64f90-150">结果会使用可配置状态代码（配置在[运行状况检查选项](#health-check-options)部分中进行介绍）编写为纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="64f90-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="64f90-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 还可以返回可选的键值对。</span><span class="sxs-lookup"><span data-stu-id="64f90-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="64f90-152">下面的 `ExampleHealthCheck` 类演示运行状况检查的布局。</span><span class="sxs-lookup"><span data-stu-id="64f90-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="64f90-153">运行状况检查逻辑位于 `CheckHealthAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="64f90-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="64f90-154">以下示例将虚拟变量 `healthCheckResultHealthy` 设为 `true`。</span><span class="sxs-lookup"><span data-stu-id="64f90-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="64f90-155">如果 `healthCheckResultHealthy` 的值设为 `false`，则返回 [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="64f90-156">注册运行状况检查服务</span><span class="sxs-lookup"><span data-stu-id="64f90-156">Register health check services</span></span>

<span data-ttu-id="64f90-157">`ExampleHealthCheck` 类型使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 添加到运行状况检查服务：</span><span class="sxs-lookup"><span data-stu-id="64f90-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="64f90-158">以下示例中显示的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 重载会设置要在运行状况检查报告失败时报告的失败状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="64f90-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="64f90-159">如果失败状态设置为 `null`（默认值），则会报告 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="64f90-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="64f90-160">此重载对于库创建者是一种十分有用的方案，在这种情况下，如果运行状况检查实现遵循该设置，则在发生运行状况检查失败时，应用会强制实施库所指示的失败状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="64f90-161">标记  用于筛选运行状况检查（在[筛选运行状况检查](#filter-health-checks)部分中进行了进一步介绍）。</span><span class="sxs-lookup"><span data-stu-id="64f90-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="64f90-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 还可以执行 lambda 函数。</span><span class="sxs-lookup"><span data-stu-id="64f90-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="64f90-163">在以下示例中，运行状况检查名称指定为 `Example`，并且检查始终返回正常状态：</span><span class="sxs-lookup"><span data-stu-id="64f90-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="64f90-164">调用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> 将参数传递到运行状况检查实现。</span><span class="sxs-lookup"><span data-stu-id="64f90-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arugments to a health check implementation.</span></span> <span data-ttu-id="64f90-165">在以下示例中，`TestHealthCheckWithArgs` 接受一个整数和一个字符串，以便在调用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 时使用：</span><span class="sxs-lookup"><span data-stu-id="64f90-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="64f90-166">`TestHealthCheckWithArgs` 通过使用传递到实现的整数和字符串调用 `AddTypeActivatedCheck` 来注册：</span><span class="sxs-lookup"><span data-stu-id="64f90-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="64f90-167">使用运行状况检查路由</span><span class="sxs-lookup"><span data-stu-id="64f90-167">Use Health Checks Routing</span></span>

<span data-ttu-id="64f90-168">在 `Startup.Configure` 内，使用终结点 URL 或相对路径在终结点生成器上调用 `MapHealthChecks`：</span><span class="sxs-lookup"><span data-stu-id="64f90-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="64f90-169">需要主机</span><span class="sxs-lookup"><span data-stu-id="64f90-169">Require host</span></span>

<span data-ttu-id="64f90-170">调用 `RequireHost` 以便为运行状况检查终结点指定一个或多个允许的主机。</span><span class="sxs-lookup"><span data-stu-id="64f90-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="64f90-171">主机应为 Unicode 而不是 punycode，且可以包含端口。</span><span class="sxs-lookup"><span data-stu-id="64f90-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="64f90-172">如果未提供集合，则接受任何主机。</span><span class="sxs-lookup"><span data-stu-id="64f90-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="64f90-173">有关详细信息，请参阅[按端口筛选](#filter-by-port)部分。</span><span class="sxs-lookup"><span data-stu-id="64f90-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="64f90-174">需要授权</span><span class="sxs-lookup"><span data-stu-id="64f90-174">Require authorization</span></span>

<span data-ttu-id="64f90-175">调用 `RequireAuthorization` 以在状况检查请求终结点上运行身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="64f90-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="64f90-176">`RequireAuthorization` 重载接受一个或多个授权策略。</span><span class="sxs-lookup"><span data-stu-id="64f90-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="64f90-177">如果未提供策略，则使用默认的授权策略。</span><span class="sxs-lookup"><span data-stu-id="64f90-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="64f90-178">启用跨域请求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="64f90-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="64f90-179">尽管从浏览器手动执行运行状况检查不是常见的使用方案，但可以通过在运行状况检查终结点上调用 `RequireCors` 来启用 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="64f90-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="64f90-180">`RequireCors` 重载接受 CORS 策略生成器委托 (`CorsPolicyBuilder`) 或策略名称。</span><span class="sxs-lookup"><span data-stu-id="64f90-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="64f90-181">如果未提供策略，则使用默认的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="64f90-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="64f90-182">有关详细信息，请参阅 <xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="64f90-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="64f90-183">运行状况检查选项</span><span class="sxs-lookup"><span data-stu-id="64f90-183">Health check options</span></span>

<span data-ttu-id="64f90-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> 使你可以自定义运行状况检查行为：</span><span class="sxs-lookup"><span data-stu-id="64f90-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="64f90-185">筛选运行状况检查</span><span class="sxs-lookup"><span data-stu-id="64f90-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="64f90-186">自定义 HTTP 状态代码</span><span class="sxs-lookup"><span data-stu-id="64f90-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="64f90-187">取消缓存标头</span><span class="sxs-lookup"><span data-stu-id="64f90-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="64f90-188">自定义输出</span><span class="sxs-lookup"><span data-stu-id="64f90-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="64f90-189">筛选运行状况检查</span><span class="sxs-lookup"><span data-stu-id="64f90-189">Filter health checks</span></span>

<span data-ttu-id="64f90-190">默认情况下，运行状况检查中间件会运行所有已注册的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="64f90-191">若要运行运行状况检查的子集，请提供向 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 选项返回布尔值的函数。</span><span class="sxs-lookup"><span data-stu-id="64f90-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="64f90-192">在以下示例中，`Bar` 运行状况检查在函数条件语句 中由于其标记 (`bar_tag`) 而被筛选掉，在条件语句中，仅当运行状况检查的 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 属性与 `foo_tag` 或 `baz_tag` 匹配时才返回 `true`：</span><span class="sxs-lookup"><span data-stu-id="64f90-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="64f90-193">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="64f90-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="64f90-194">在 `Startup.Configure` 中，`Predicate` 筛选出“Bar”运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="64f90-195">仅 Foo 和 Baz 执行：</span><span class="sxs-lookup"><span data-stu-id="64f90-195">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="64f90-196">自定义 HTTP 状态代码</span><span class="sxs-lookup"><span data-stu-id="64f90-196">Customize the HTTP status code</span></span>

<span data-ttu-id="64f90-197">使用 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> 可自定义运行状况状态到 HTTP 状态代码的映射。</span><span class="sxs-lookup"><span data-stu-id="64f90-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="64f90-198">以下 <xref:Microsoft.AspNetCore.Http.StatusCodes> 分配是中间件所使用的默认值。</span><span class="sxs-lookup"><span data-stu-id="64f90-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="64f90-199">更改状态代码值以满足要求。</span><span class="sxs-lookup"><span data-stu-id="64f90-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="64f90-200">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="64f90-200">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="64f90-201">取消缓存标头</span><span class="sxs-lookup"><span data-stu-id="64f90-201">Suppress cache headers</span></span>

<span data-ttu-id="64f90-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> 控制运行状况检查中间件是否将 HTTP 标头添加到探测响应以防止响应缓存。</span><span class="sxs-lookup"><span data-stu-id="64f90-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="64f90-203">如果值为 `false`（默认值），则中间件会设置或替代 `Cache-Control`、`Expires` 和 `Pragma` 标头以防止响应缓存。</span><span class="sxs-lookup"><span data-stu-id="64f90-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="64f90-204">如果值为 `true`，则中间件不会修改响应的缓存标头。</span><span class="sxs-lookup"><span data-stu-id="64f90-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="64f90-205">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="64f90-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="64f90-206">自定义输出</span><span class="sxs-lookup"><span data-stu-id="64f90-206">Customize output</span></span>

<span data-ttu-id="64f90-207"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 选项可获取或设置用于编写响应的委托。</span><span class="sxs-lookup"><span data-stu-id="64f90-207">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span>

<span data-ttu-id="64f90-208">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="64f90-208">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="64f90-209">默认委托会使用 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 字符串值编写最小的纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="64f90-209">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="64f90-210">以下自定义委托 `WriteResponse` 输出自定义 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-210">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
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

<span data-ttu-id="64f90-211">运行状况检查系统不为复杂 JSON 返回格式提供内置支持，因为该格式特定于你选择的监视系统。</span><span class="sxs-lookup"><span data-stu-id="64f90-211">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="64f90-212">可以根据需要在前面的示例中随意自定义 `JObject` 以满足你的需求。</span><span class="sxs-lookup"><span data-stu-id="64f90-212">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="64f90-213">数据库探测</span><span class="sxs-lookup"><span data-stu-id="64f90-213">Database probe</span></span>

<span data-ttu-id="64f90-214">运行状况检查可以指定数据库查询作为布尔测试来运行，以指示数据库是否在正常响应。</span><span class="sxs-lookup"><span data-stu-id="64f90-214">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="64f90-215">示例应用使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)（ASP.NET Core 应用的运行状况检查库）对 SQL Server 数据库执行运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-215">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="64f90-216">`AspNetCore.Diagnostics.HealthChecks` 对数据库执行 `SELECT 1` 查询以确认与数据库的连接是否正常。</span><span class="sxs-lookup"><span data-stu-id="64f90-216">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="64f90-217">使用查询检查数据库连接时，请选择快速返回的查询。</span><span class="sxs-lookup"><span data-stu-id="64f90-217">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="64f90-218">查询方法会面临使数据库过载和降低其性能的风险。</span><span class="sxs-lookup"><span data-stu-id="64f90-218">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="64f90-219">在大多数情况下，无需运行测试查询。</span><span class="sxs-lookup"><span data-stu-id="64f90-219">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="64f90-220">只需建立成功的数据库连接便足矣。</span><span class="sxs-lookup"><span data-stu-id="64f90-220">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="64f90-221">如果发现需要运行查询，请选择简单的 SELECT 查询，如 `SELECT 1`。</span><span class="sxs-lookup"><span data-stu-id="64f90-221">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="64f90-222">包括对 [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="64f90-222">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="64f90-223">在应用的 appsettings.json  文件中提供有效数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="64f90-223">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="64f90-224">应用使用名为 `HealthCheckSample` 的 SQL Server 数据库：</span><span class="sxs-lookup"><span data-stu-id="64f90-224">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="64f90-225">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="64f90-225">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="64f90-226">示例应用使用数据库的连接字符串 (DbHealthStartup.cs  ) 调用 `AddSqlServer` 方法：</span><span class="sxs-lookup"><span data-stu-id="64f90-226">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="64f90-227">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="64f90-227">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="64f90-228">若要使用示例应用运行数据库探测方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-228">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="64f90-229">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="64f90-229">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="64f90-230">Entity Framework Core DbContext 探测</span><span class="sxs-lookup"><span data-stu-id="64f90-230">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="64f90-231">`DbContext` 检查确认应用可以与为 EF Core `DbContext` 配置的数据库通信。</span><span class="sxs-lookup"><span data-stu-id="64f90-231">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="64f90-232">满足以下条件的应用支持 `DbContext` 检查：</span><span class="sxs-lookup"><span data-stu-id="64f90-232">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="64f90-233">使用 [Entity Framework (EF) Core](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="64f90-233">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="64f90-234">包括对 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="64f90-234">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="64f90-235">`AddDbContextCheck<TContext>` 为 `DbContext` 注册运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-235">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="64f90-236">`DbContext` 作为方法的 `TContext` 提供。</span><span class="sxs-lookup"><span data-stu-id="64f90-236">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="64f90-237">重载可用于配置失败状态、标记和自定义测试查询。</span><span class="sxs-lookup"><span data-stu-id="64f90-237">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="64f90-238">默认情况下：</span><span class="sxs-lookup"><span data-stu-id="64f90-238">By default:</span></span>

* <span data-ttu-id="64f90-239">`DbContextHealthCheck` 调用 EF Core 的 `CanConnectAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="64f90-239">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="64f90-240">可以自定义在使用 `AddDbContextCheck` 方法重载检查运行状况时运行的操作。</span><span class="sxs-lookup"><span data-stu-id="64f90-240">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="64f90-241">运行状况检查的名称是 `TContext` 类型的名称。</span><span class="sxs-lookup"><span data-stu-id="64f90-241">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="64f90-242">在示例应用中，`AppDbContext` 会提供给 `AddDbContextCheck`，并在 `Startup.ConfigureServices` 中注册为服务 (DbContextHealthStartup.cs  )：</span><span class="sxs-lookup"><span data-stu-id="64f90-242">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="64f90-243">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="64f90-243">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="64f90-244">若要使用示例应用运行 `DbContext` 探测方案，请确认连接字符串指定的数据库在 SQL Server 实例中不存在。</span><span class="sxs-lookup"><span data-stu-id="64f90-244">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="64f90-245">如果该数据库存在，请删除它。</span><span class="sxs-lookup"><span data-stu-id="64f90-245">If the database exists, delete it.</span></span>

<span data-ttu-id="64f90-246">在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-246">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="64f90-247">在应用运行之后，在浏览器中对 `/health` 终结点发出请求，从而检查运行状况。</span><span class="sxs-lookup"><span data-stu-id="64f90-247">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="64f90-248">数据库和 `AppDbContext` 不存在，因此应用提供以下响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-248">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="64f90-249">触发示例应用以创建数据库。</span><span class="sxs-lookup"><span data-stu-id="64f90-249">Trigger the sample app to create the database.</span></span> <span data-ttu-id="64f90-250">向 `/createdatabase` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-250">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="64f90-251">应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-251">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="64f90-252">向 `/health` 终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-252">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="64f90-253">数据库和上下文存在，因此应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-253">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="64f90-254">触发示例应用以删除数据库。</span><span class="sxs-lookup"><span data-stu-id="64f90-254">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="64f90-255">向 `/deletedatabase` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-255">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="64f90-256">应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-256">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="64f90-257">向 `/health` 终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-257">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="64f90-258">应用提供不正常的响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-258">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="64f90-259">单独的就绪情况和运行情况探测</span><span class="sxs-lookup"><span data-stu-id="64f90-259">Separate readiness and liveness probes</span></span>

<span data-ttu-id="64f90-260">在某些托管方案中，会使用一对区分两种应用状态的运行状况检查：</span><span class="sxs-lookup"><span data-stu-id="64f90-260">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="64f90-261">应用正常运行，但尚未准备好接收请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-261">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="64f90-262">此状态是应用的就绪情况  。</span><span class="sxs-lookup"><span data-stu-id="64f90-262">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="64f90-263">应用正常运行并响应请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-263">The app is functioning and responding to requests.</span></span> <span data-ttu-id="64f90-264">此状态是应用的运行情况  。</span><span class="sxs-lookup"><span data-stu-id="64f90-264">This state is the app's *liveness*.</span></span>

<span data-ttu-id="64f90-265">就绪情况检查通常执行更广泛和耗时的检查集，以确定应用的所有子系统和资源是否都可用。</span><span class="sxs-lookup"><span data-stu-id="64f90-265">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="64f90-266">运行情况检查只是执行一个快速检查，以确定应用是否可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-266">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="64f90-267">应用通过其就绪情况检查之后，无需使用成本高昂的就绪情况检查集来进一步增加应用负荷 &mdash; 后续检查只需检查运行情况。</span><span class="sxs-lookup"><span data-stu-id="64f90-267">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="64f90-268">示例应用包含运行状况检查，以报告[托管服务](xref:fundamentals/host/hosted-services)中长时间运行的启动任务的完成。</span><span class="sxs-lookup"><span data-stu-id="64f90-268">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="64f90-269">`StartupHostedServiceHealthCheck` 公开了属性 `StartupTaskCompleted`，托管服务在其长时间运行的任务完成时可以将该属性设置为 `true` (StartupHostedServiceHealthCheck.cs  )：</span><span class="sxs-lookup"><span data-stu-id="64f90-269">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="64f90-270">长时间运行的后台任务由[托管服务](xref:fundamentals/host/hosted-services) (Services/StartupHostedService  ) 启动。</span><span class="sxs-lookup"><span data-stu-id="64f90-270">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="64f90-271">在该任务结束时，`StartupHostedServiceHealthCheck.StartupTaskCompleted` 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="64f90-271">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="64f90-272">运行状况检查在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 与托管服务一起注册。</span><span class="sxs-lookup"><span data-stu-id="64f90-272">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="64f90-273">因为托管服务必须对运行状况检查设置该属性，所以运行状况检查也会在服务容器 (LivenessProbeStartup.cs  ) 中进行注册：</span><span class="sxs-lookup"><span data-stu-id="64f90-273">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="64f90-274">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="64f90-274">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="64f90-275">在示例应用中，在以下位置创建运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="64f90-275">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="64f90-276">`/health/ready`（用于就绪状态检查）。</span><span class="sxs-lookup"><span data-stu-id="64f90-276">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="64f90-277">就绪情况检查会将运行状况检查筛选到具有 `ready` 标记的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-277">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="64f90-278">`/health/live`（用于运行情况检查）。</span><span class="sxs-lookup"><span data-stu-id="64f90-278">`/health/live` for the liveness check.</span></span> <span data-ttu-id="64f90-279">运行情况检查通过在 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) 中返回 `false` 来筛选出 `StartupHostedServiceHealthCheck`（有关详细信息，请参阅[筛选运行状况检查](#filter-health-checks)）</span><span class="sxs-lookup"><span data-stu-id="64f90-279">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="64f90-280">在以下示例代码中：</span><span class="sxs-lookup"><span data-stu-id="64f90-280">In the following example code:</span></span>

* <span data-ttu-id="64f90-281">就绪状态检查将所有已注册的检查与“ready”标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="64f90-281">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="64f90-282">`Predicate` 将排除所有检查并返回 200-Ok。</span><span class="sxs-lookup"><span data-stu-id="64f90-282">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="64f90-283">若要使用示例应用运行就绪情况/运行情况配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-283">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="64f90-284">在浏览器中，访问 `/health/ready` 几次，直到过了 15 秒。</span><span class="sxs-lookup"><span data-stu-id="64f90-284">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="64f90-285">运行状况检查会在前 15 秒内报告“运行不正常”  。</span><span class="sxs-lookup"><span data-stu-id="64f90-285">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="64f90-286">15 秒之后，终结点会报告“运行正常”  ，这反映托管服务完成了长时间运行的任务。</span><span class="sxs-lookup"><span data-stu-id="64f90-286">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="64f90-287">此示例还创建了一个运行第一个就绪检查的运行状况检查发布服务器（<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现），延迟时间为两秒。</span><span class="sxs-lookup"><span data-stu-id="64f90-287">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="64f90-288">有关详细信息，请参阅[运行状况检查发布服务器](#health-check-publisher)部分。</span><span class="sxs-lookup"><span data-stu-id="64f90-288">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="64f90-289">Kubernetes 示例</span><span class="sxs-lookup"><span data-stu-id="64f90-289">Kubernetes example</span></span>

<span data-ttu-id="64f90-290">在诸如 [Kubernetes](https://kubernetes.io/) 这类环境中，使用单独的就绪情况和运行情况检查会十分有用。</span><span class="sxs-lookup"><span data-stu-id="64f90-290">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="64f90-291">在 Kubernetes 中，应用可能需要在接受请求之前执行耗时的启动工作，如基础数据库可用性测试。</span><span class="sxs-lookup"><span data-stu-id="64f90-291">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="64f90-292">使用单独检查使业务流程协调程序可以区分应用是否正常运行但尚未准备就绪，或是应用程序是否未能启动。</span><span class="sxs-lookup"><span data-stu-id="64f90-292">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="64f90-293">有关 Kubernetes 中的就绪情况和运行情况探测的详细信息，请参阅 Kubernetes 文档中的[配置运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。</span><span class="sxs-lookup"><span data-stu-id="64f90-293">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="64f90-294">以下示例演示如何使用 Kubernetes 就绪情况探测配置：</span><span class="sxs-lookup"><span data-stu-id="64f90-294">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="64f90-295">具有自定义响应编写器的基于指标的探测</span><span class="sxs-lookup"><span data-stu-id="64f90-295">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="64f90-296">示例应用演示具有自定义响应编写器的内存运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-296">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="64f90-297">如果应用使用的内存多于给定内存阈值（在示例应用中为 1 GB），则 `MemoryHealthCheck` 报告降级状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-297">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="64f90-298"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 包括应用的垃圾回收器 (GC) 信息 (MemoryHealthCheck.cs  )：</span><span class="sxs-lookup"><span data-stu-id="64f90-298">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="64f90-299">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="64f90-299">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="64f90-300">`MemoryHealthCheck` 注册为服务，而不是通过将运行状况检查传递到 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 来启用它。</span><span class="sxs-lookup"><span data-stu-id="64f90-300">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="64f90-301">所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 注册服务都可供运行状况检查服务和中间件使用。</span><span class="sxs-lookup"><span data-stu-id="64f90-301">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="64f90-302">建议将运行状况检查服务注册为单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="64f90-302">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="64f90-303">在示例应用 (CustomWriterStartup.cs  ) 中：</span><span class="sxs-lookup"><span data-stu-id="64f90-303">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="64f90-304">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="64f90-304">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="64f90-305">一个 `WriteResponse` 委托提供给 `ResponseWriter` 属性，以在执行运行状况检查时输出自定义 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-305">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="64f90-306">`WriteResponse` 方法将 `CompositeHealthCheckResult` 格式化为 JSON 对象，并生成运行状况检查响应的 JSON 输出：</span><span class="sxs-lookup"><span data-stu-id="64f90-306">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="64f90-307">若要使用示例应用运行具有自定义响应编写器输出的基于指标的探测，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-307">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="64f90-308">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包括基于指标的运行状况检查方案（包括磁盘存储和最大值运行情况检查）。</span><span class="sxs-lookup"><span data-stu-id="64f90-308">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="64f90-309">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="64f90-309">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="64f90-310">按端口筛选</span><span class="sxs-lookup"><span data-stu-id="64f90-310">Filter by port</span></span>

<span data-ttu-id="64f90-311">使用 URL 模式在 `MapHealthChecks` 上调用 `RequireHost`，该 URL 模式指定一个端口，以使运行状况检查请求限于指定端口。</span><span class="sxs-lookup"><span data-stu-id="64f90-311">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="64f90-312">这通常用于在容器环境中公开用于监视服务的端口。</span><span class="sxs-lookup"><span data-stu-id="64f90-312">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="64f90-313">示例应用使用[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)配置端口。</span><span class="sxs-lookup"><span data-stu-id="64f90-313">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="64f90-314">端口在 launchSettings.json  文件设置，并通过环境变量传递到配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="64f90-314">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="64f90-315">还必须配置服务器以在管理端口上侦听请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-315">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="64f90-316">若要使用示例应用演示管理端口配置，请在 Properties  文件夹中创建 launchSettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="64f90-316">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="64f90-317">示例应用中的以下 Properties/launchSettings.json  文件未包含在示例应用的项目文件中，必须手动创建：</span><span class="sxs-lookup"><span data-stu-id="64f90-317">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="64f90-318">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="64f90-318">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="64f90-319">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="64f90-319">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="64f90-320">在示例应用中，在 `Startup.Configure` 中的终结点上调用 `RequireHost` 将从配置中指定管理端口：</span><span class="sxs-lookup"><span data-stu-id="64f90-320">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="64f90-321">在示例应用中，在 `Startup.Configure` 中创建终结点。</span><span class="sxs-lookup"><span data-stu-id="64f90-321">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="64f90-322">在以下示例代码中：</span><span class="sxs-lookup"><span data-stu-id="64f90-322">In the following example code:</span></span>

* <span data-ttu-id="64f90-323">就绪状态检查将所有已注册的检查与“ready”标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="64f90-323">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="64f90-324">`Predicate` 将排除所有检查并返回 200-Ok。</span><span class="sxs-lookup"><span data-stu-id="64f90-324">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="64f90-325">可以通过在代码中显式设置管理端口，来避免在示例应用中创建 launchSettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="64f90-325">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="64f90-326">在创建 <xref:Microsoft.Extensions.Hosting.HostBuilder> 的 Program.cs  中，添加对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> 的调用并提供应用的管理端口终结点。</span><span class="sxs-lookup"><span data-stu-id="64f90-326">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="64f90-327">在 ManagementPortStartup.cs  的 `Configure` 中，使用 `RequireHost` 指定管理端口：</span><span class="sxs-lookup"><span data-stu-id="64f90-327">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="64f90-328">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="64f90-328">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="64f90-329">ManagementPortStartup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="64f90-329">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="64f90-330">若要使用示例应用运行管理端口配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-330">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="64f90-331">分发运行状况检查库</span><span class="sxs-lookup"><span data-stu-id="64f90-331">Distribute a health check library</span></span>

<span data-ttu-id="64f90-332">将运行状况检查作为库进行分发：</span><span class="sxs-lookup"><span data-stu-id="64f90-332">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="64f90-333">编写将 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 接口作为独立类来实现的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-333">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="64f90-334">该类可以依赖于[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)类型激活和[命名选项](xref:fundamentals/configuration/options)来访问配置数据。</span><span class="sxs-lookup"><span data-stu-id="64f90-334">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="64f90-335">在 `CheckHealthAsync` 的运行状况检查逻辑中：</span><span class="sxs-lookup"><span data-stu-id="64f90-335">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="64f90-336">方法中使用 `data1` 和 `data2` 运行探测的运行状况检查逻辑。</span><span class="sxs-lookup"><span data-stu-id="64f90-336">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="64f90-337">`AccessViolationException` 已处理。</span><span class="sxs-lookup"><span data-stu-id="64f90-337">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="64f90-338">发生 <xref:System.AccessViolationException> 时，将返回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> 和 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，以允许用户配置运行状况检查失败状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-338">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="64f90-339">使用参数编写一个扩展方法，所使用的应用会在其 `Startup.Configure` 方法中调用它。</span><span class="sxs-lookup"><span data-stu-id="64f90-339">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="64f90-340">在以下示例中，假设以下运行状况检查方法签名：</span><span class="sxs-lookup"><span data-stu-id="64f90-340">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="64f90-341">前面的签名指示 `ExampleHealthCheck` 需要其他数据来处理运行状况检查探测逻辑。</span><span class="sxs-lookup"><span data-stu-id="64f90-341">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="64f90-342">当运行状况检查向扩展方法注册时，数据会提供给用于创建运行状况检查实例的委托。</span><span class="sxs-lookup"><span data-stu-id="64f90-342">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="64f90-343">在以下示例中，调用方会指定可选的：</span><span class="sxs-lookup"><span data-stu-id="64f90-343">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="64f90-344">运行状况检查名称 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="64f90-344">health check name (`name`).</span></span> <span data-ttu-id="64f90-345">如果为 `null`，则使用 `example_health_check`。</span><span class="sxs-lookup"><span data-stu-id="64f90-345">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="64f90-346">运行状况检查的字符串数据点 (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="64f90-346">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="64f90-347">运行状况检查的整数数据点 (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="64f90-347">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="64f90-348">如果为 `null`，则使用 `1`。</span><span class="sxs-lookup"><span data-stu-id="64f90-348">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="64f90-349">失败状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="64f90-349">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="64f90-350">默认值为 `null`。</span><span class="sxs-lookup"><span data-stu-id="64f90-350">The default is `null`.</span></span> <span data-ttu-id="64f90-351">如果为 `null`，则报告失败状态 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="64f90-351">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="64f90-352">标记 (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="64f90-352">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="64f90-353">运行状况检查发布服务器</span><span class="sxs-lookup"><span data-stu-id="64f90-353">Health Check Publisher</span></span>

<span data-ttu-id="64f90-354">当 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 添加到服务容器时，运行状况检查系统，会定期执行运行状况检查并使用结果调用 `PublishAsync`。</span><span class="sxs-lookup"><span data-stu-id="64f90-354">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="64f90-355">在期望每个进程定期调用监视系统以便确定运行状况的基于推送的运行状况监视系统方案中，这十分有用。</span><span class="sxs-lookup"><span data-stu-id="64f90-355">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="64f90-356"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 接口具有单个方法：</span><span class="sxs-lookup"><span data-stu-id="64f90-356">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="64f90-357">使用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> 可设置：</span><span class="sxs-lookup"><span data-stu-id="64f90-357"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="64f90-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; 在应用程序启动后且在应用程序执行 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例之前所应用的初始延迟。</span><span class="sxs-lookup"><span data-stu-id="64f90-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="64f90-359">延迟在启动时应用一次，不适用于后续迭代。</span><span class="sxs-lookup"><span data-stu-id="64f90-359">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="64f90-360">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="64f90-360">The default value is five seconds.</span></span>
* <span data-ttu-id="64f90-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 执行的时间。</span><span class="sxs-lookup"><span data-stu-id="64f90-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="64f90-362">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="64f90-362">The default value is 30 seconds.</span></span>
* <span data-ttu-id="64f90-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; 如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> 为 `null`（默认值），则运行状况检查发布服务器服务运行所有已注册的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="64f90-364">若要运行运行状况检查的子集，请提供用于筛选检查集的函数。</span><span class="sxs-lookup"><span data-stu-id="64f90-364">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="64f90-365">每个时间段都会评估谓词。</span><span class="sxs-lookup"><span data-stu-id="64f90-365">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="64f90-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; 执行所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例的运行状况检查的超时时间。</span><span class="sxs-lookup"><span data-stu-id="64f90-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="64f90-367">在不超时的情况下，使用 <xref:System.Threading.Timeout.InfiniteTimeSpan> 执行。</span><span class="sxs-lookup"><span data-stu-id="64f90-367">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="64f90-368">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="64f90-368">The default value is 30 seconds.</span></span>

<span data-ttu-id="64f90-369">在示例应用中，`ReadinessPublisher` 是 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现。</span><span class="sxs-lookup"><span data-stu-id="64f90-369">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="64f90-370">在以下日志级别，针对每次检查记录运行状况检查状态：</span><span class="sxs-lookup"><span data-stu-id="64f90-370">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="64f90-371">如果运行状况检查状态为 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>，则为信息 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>)。</span><span class="sxs-lookup"><span data-stu-id="64f90-371">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="64f90-372">如果状态为 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 或 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>，则为错误 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>)。</span><span class="sxs-lookup"><span data-stu-id="64f90-372">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="64f90-373">在示例应用的 `LivenessProbeStartup` 示例中，`StartupHostedService` 就绪状态检查有两秒的启动延迟，并且每 30 秒运行一次检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-373">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="64f90-374">为激活 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现，示例将 `ReadinessPublisher` 注册为[依存关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中的单一实例服务：</span><span class="sxs-lookup"><span data-stu-id="64f90-374">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="64f90-375">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包括多个系统的发布服务器（包括 [Application Insights](/azure/application-insights/app-insights-overview)）。</span><span class="sxs-lookup"><span data-stu-id="64f90-375">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="64f90-376">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="64f90-376">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="64f90-377">使用 MapWhen 限制运行状况检查</span><span class="sxs-lookup"><span data-stu-id="64f90-377">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="64f90-378">使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 对运行状况检查终结点的请求管道进行条件分支。</span><span class="sxs-lookup"><span data-stu-id="64f90-378">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="64f90-379">在以下示例中，如果收到 `api/HealthCheck` 终结点的 GET 请求，`MapWhen` 将对请求管道进行分支以激活运行状况检查中间件：</span><span class="sxs-lookup"><span data-stu-id="64f90-379">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="64f90-380">有关详细信息，请参阅 <xref:fundamentals/middleware/index#use-run-and-map>。</span><span class="sxs-lookup"><span data-stu-id="64f90-380">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="64f90-381">ASP.NET Core 提供运行状况检查中间件和库，以用于报告应用基础结构组件的运行状况。</span><span class="sxs-lookup"><span data-stu-id="64f90-381">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="64f90-382">运行状况检查由应用程序作为 HTTP 终结点公开。</span><span class="sxs-lookup"><span data-stu-id="64f90-382">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="64f90-383">可以为各种实时监视方案配置运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="64f90-383">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="64f90-384">运行状况探测可以由容器业务流程协调程和负载均衡器用于检查应用的状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-384">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="64f90-385">例如，容器业务流程协调程序可以通过停止滚动部署或重新启动容器来响应失败的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-385">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="64f90-386">负载均衡器可以通过将流量从失败的实例路由到正常实例，来应对不正常的应用。</span><span class="sxs-lookup"><span data-stu-id="64f90-386">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="64f90-387">可以监视内存、磁盘和其他物理服务器资源的使用情况来了解是否处于正常状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-387">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="64f90-388">运行状况检查可以测试应用的依赖项（如数据库和外部服务终结点）以确认是否可用和正常工作。</span><span class="sxs-lookup"><span data-stu-id="64f90-388">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="64f90-389">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="64f90-389">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="64f90-390">示例应用包含本主题中所述的方案示例。</span><span class="sxs-lookup"><span data-stu-id="64f90-390">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="64f90-391">若要运行给定方案的示例应用，请在命令行界面中从项目文件夹中使用 [dotnet run](/dotnet/core/tools/dotnet-run) 命令。</span><span class="sxs-lookup"><span data-stu-id="64f90-391">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="64f90-392">请参阅示例应用的 README.md  文件和本主题中的方案说明，以了解有关如何使用示例应用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="64f90-392">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="64f90-393">系统必备</span><span class="sxs-lookup"><span data-stu-id="64f90-393">Prerequisites</span></span>

<span data-ttu-id="64f90-394">运行状况检查通常与外部监视服务或容器业务流程协调程序一起用于检查应用的状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-394">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="64f90-395">向应用添加运行状况检查之前，需确定要使用的监视系统。</span><span class="sxs-lookup"><span data-stu-id="64f90-395">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="64f90-396">监视系统决定了要创建的运行状况检查类型以及配置其终结点的方式。</span><span class="sxs-lookup"><span data-stu-id="64f90-396">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="64f90-397">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 包。</span><span class="sxs-lookup"><span data-stu-id="64f90-397">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="64f90-398">示例应用提供了启动代码来演示几个方案的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-398">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="64f90-399">[数据库探测](#database-probe)方案使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 检查数据库连接的运行状况。</span><span class="sxs-lookup"><span data-stu-id="64f90-399">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="64f90-400">[DbContext 探测](#entity-framework-core-dbcontext-probe)方案使用 EF Core `DbContext` 检查数据库。</span><span class="sxs-lookup"><span data-stu-id="64f90-400">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="64f90-401">若要探索数据库方案，示例应用将：</span><span class="sxs-lookup"><span data-stu-id="64f90-401">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="64f90-402">创建一个数据库，并在 appsettings.json 文件中提供其连接字符串  。</span><span class="sxs-lookup"><span data-stu-id="64f90-402">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="64f90-403">其项目文件中具有以下包引用：</span><span class="sxs-lookup"><span data-stu-id="64f90-403">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="64f90-404">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="64f90-404">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="64f90-405">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="64f90-405">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="64f90-406">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="64f90-406">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="64f90-407">另一个运行状况检查方案演示如何将运行状况检查筛选到某个管理端口。</span><span class="sxs-lookup"><span data-stu-id="64f90-407">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="64f90-408">示例应用要求创建包含管理 URL 和管理端口的 Properties/launchSettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="64f90-408">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="64f90-409">有关详细信息，请参阅[按端口筛选](#filter-by-port)部分。</span><span class="sxs-lookup"><span data-stu-id="64f90-409">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="64f90-410">基本运行状况探测</span><span class="sxs-lookup"><span data-stu-id="64f90-410">Basic health probe</span></span>

<span data-ttu-id="64f90-411">对于许多应用，报告应用在处理请求方面的可用性（运行情况  ）的基本运行状况探测配置足以发现应用的状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-411">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="64f90-412">基本配置会注册运行状况检查服务，并调用运行状况检查中间件以通过运行状况响应在 URL 终结点处进行响应。</span><span class="sxs-lookup"><span data-stu-id="64f90-412">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="64f90-413">默认情况下，不会注册任何特定运行状况检查来测试任何特定依赖项或子系统。</span><span class="sxs-lookup"><span data-stu-id="64f90-413">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="64f90-414">如果能够在运行状况终结点 URL 处进行响应，则应用被视为正常。</span><span class="sxs-lookup"><span data-stu-id="64f90-414">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="64f90-415">默认响应编写器会以纯文本响应形式将状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) 写回到客户端，以便指示 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 或 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-415">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="64f90-416">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="64f90-416">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="64f90-417">在 `Startup.Configure` 的请求处理管道中，使用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 为运行状况检查中间件添加终结点。</span><span class="sxs-lookup"><span data-stu-id="64f90-417">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="64f90-418">在示例应用中，在 `/health` 处创建运行状况检查终结点 (BasicStartup.cs  )：</span><span class="sxs-lookup"><span data-stu-id="64f90-418">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="64f90-419">若要使用示例应用运行基本配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-419">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="64f90-420">Docker 示例</span><span class="sxs-lookup"><span data-stu-id="64f90-420">Docker example</span></span>

<span data-ttu-id="64f90-421">[Docker](xref:host-and-deploy/docker/index) 提供内置 `HEALTHCHECK` 指令，该指令可以用于检查使用基本运行状况检查配置的应用的状态：</span><span class="sxs-lookup"><span data-stu-id="64f90-421">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="64f90-422">创建运行状况检查</span><span class="sxs-lookup"><span data-stu-id="64f90-422">Create health checks</span></span>

<span data-ttu-id="64f90-423">运行状况检查通过实现 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 接口进行创建。</span><span class="sxs-lookup"><span data-stu-id="64f90-423">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="64f90-424"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 方法会返回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，它以 `Healthy`、`Degraded` 或 `Unhealthy` 的形式指示运行状况。</span><span class="sxs-lookup"><span data-stu-id="64f90-424">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="64f90-425">结果会使用可配置状态代码（配置在[运行状况检查选项](#health-check-options)部分中进行介绍）编写为纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="64f90-425">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="64f90-426"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 还可以返回可选的键值对。</span><span class="sxs-lookup"><span data-stu-id="64f90-426"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="64f90-427">示例运行状况检查</span><span class="sxs-lookup"><span data-stu-id="64f90-427">Example health check</span></span>

<span data-ttu-id="64f90-428">下面的 `ExampleHealthCheck` 类演示运行状况检查的布局。</span><span class="sxs-lookup"><span data-stu-id="64f90-428">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="64f90-429">运行状况检查逻辑位于 `CheckHealthAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="64f90-429">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="64f90-430">以下示例将虚拟变量 `healthCheckResultHealthy` 设为 `true`。</span><span class="sxs-lookup"><span data-stu-id="64f90-430">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="64f90-431">如果 `healthCheckResultHealthy` 的值设为 `false`，则返回 [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-431">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
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

### <a name="register-health-check-services"></a><span data-ttu-id="64f90-432">注册运行状况检查服务</span><span class="sxs-lookup"><span data-stu-id="64f90-432">Register health check services</span></span>

<span data-ttu-id="64f90-433">`ExampleHealthCheck` 类型在 `Startup.ConfigureServices` 中通过 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 添加到运行状况检查服务：</span><span class="sxs-lookup"><span data-stu-id="64f90-433">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="64f90-434">以下示例中显示的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 重载会设置要在运行状况检查报告失败时报告的失败状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="64f90-434">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="64f90-435">如果失败状态设置为 `null`（默认值），则会报告 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="64f90-435">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="64f90-436">此重载对于库创建者是一种十分有用的方案，在这种情况下，如果运行状况检查实现遵循该设置，则在发生运行状况检查失败时，应用会强制实施库所指示的失败状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-436">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="64f90-437">标记  用于筛选运行状况检查（在[筛选运行状况检查](#filter-health-checks)部分中进行了进一步介绍）。</span><span class="sxs-lookup"><span data-stu-id="64f90-437">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="64f90-438"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 还可以执行 lambda 函数。</span><span class="sxs-lookup"><span data-stu-id="64f90-438"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="64f90-439">在以下 `Startup.ConfigureServices` 示例中，运行状况检查名称指定为 `Example`，并且检查始终返回正常状态：</span><span class="sxs-lookup"><span data-stu-id="64f90-439">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="64f90-440">使用运行状况检查中间件</span><span class="sxs-lookup"><span data-stu-id="64f90-440">Use Health Checks Middleware</span></span>

<span data-ttu-id="64f90-441">在 `Startup.Configure` 内，在处理管道中使用终结点 URL 或相对路径调用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>：</span><span class="sxs-lookup"><span data-stu-id="64f90-441">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="64f90-442">如果运行状况检查应侦听特定端口，则使用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 的重载设置端口（在[按端口筛选](#filter-by-port)部分中进行了进一步介绍）：</span><span class="sxs-lookup"><span data-stu-id="64f90-442">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="64f90-443">运行状况检查选项</span><span class="sxs-lookup"><span data-stu-id="64f90-443">Health check options</span></span>

<span data-ttu-id="64f90-444"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> 使你可以自定义运行状况检查行为：</span><span class="sxs-lookup"><span data-stu-id="64f90-444"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="64f90-445">筛选运行状况检查</span><span class="sxs-lookup"><span data-stu-id="64f90-445">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="64f90-446">自定义 HTTP 状态代码</span><span class="sxs-lookup"><span data-stu-id="64f90-446">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="64f90-447">取消缓存标头</span><span class="sxs-lookup"><span data-stu-id="64f90-447">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="64f90-448">自定义输出</span><span class="sxs-lookup"><span data-stu-id="64f90-448">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="64f90-449">筛选运行状况检查</span><span class="sxs-lookup"><span data-stu-id="64f90-449">Filter health checks</span></span>

<span data-ttu-id="64f90-450">默认情况下，运行状况检查中间件会运行所有已注册的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-450">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="64f90-451">若要运行运行状况检查的子集，请提供向 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 选项返回布尔值的函数。</span><span class="sxs-lookup"><span data-stu-id="64f90-451">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="64f90-452">在以下示例中，`Bar` 运行状况检查在函数条件语句 中由于其标记 (`bar_tag`) 而被筛选掉，在条件语句中，仅当运行状况检查的 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 属性与 `foo_tag` 或 `baz_tag` 匹配时才返回 `true`：</span><span class="sxs-lookup"><span data-stu-id="64f90-452">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="64f90-453">自定义 HTTP 状态代码</span><span class="sxs-lookup"><span data-stu-id="64f90-453">Customize the HTTP status code</span></span>

<span data-ttu-id="64f90-454">使用 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> 可自定义运行状况状态到 HTTP 状态代码的映射。</span><span class="sxs-lookup"><span data-stu-id="64f90-454">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="64f90-455">以下 <xref:Microsoft.AspNetCore.Http.StatusCodes> 分配是中间件所使用的默认值。</span><span class="sxs-lookup"><span data-stu-id="64f90-455">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="64f90-456">更改状态代码值以满足要求。</span><span class="sxs-lookup"><span data-stu-id="64f90-456">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="64f90-457">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="64f90-457">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="64f90-458">取消缓存标头</span><span class="sxs-lookup"><span data-stu-id="64f90-458">Suppress cache headers</span></span>

<span data-ttu-id="64f90-459"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> 控制运行状况检查中间件是否将 HTTP 标头添加到探测响应以防止响应缓存。</span><span class="sxs-lookup"><span data-stu-id="64f90-459"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="64f90-460">如果值为 `false`（默认值），则中间件会设置或替代 `Cache-Control`、`Expires` 和 `Pragma` 标头以防止响应缓存。</span><span class="sxs-lookup"><span data-stu-id="64f90-460">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="64f90-461">如果值为 `true`，则中间件不会修改响应的缓存标头。</span><span class="sxs-lookup"><span data-stu-id="64f90-461">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="64f90-462">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="64f90-462">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="64f90-463">自定义输出</span><span class="sxs-lookup"><span data-stu-id="64f90-463">Customize output</span></span>

<span data-ttu-id="64f90-464"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 选项可获取或设置用于编写响应的委托。</span><span class="sxs-lookup"><span data-stu-id="64f90-464">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="64f90-465">默认委托会使用 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 字符串值编写最小的纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="64f90-465">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="64f90-466">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="64f90-466">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="64f90-467">默认委托会使用 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 字符串值编写最小的纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="64f90-467">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="64f90-468">以下自定义委托 `WriteResponse` 输出自定义 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-468">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
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

<span data-ttu-id="64f90-469">运行状况检查系统不为复杂 JSON 返回格式提供内置支持，因为该格式特定于你选择的监视系统。</span><span class="sxs-lookup"><span data-stu-id="64f90-469">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="64f90-470">可以根据需要在前面的示例中随意自定义 `JObject` 以满足你的需求。</span><span class="sxs-lookup"><span data-stu-id="64f90-470">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="64f90-471">数据库探测</span><span class="sxs-lookup"><span data-stu-id="64f90-471">Database probe</span></span>

<span data-ttu-id="64f90-472">运行状况检查可以指定数据库查询作为布尔测试来运行，以指示数据库是否在正常响应。</span><span class="sxs-lookup"><span data-stu-id="64f90-472">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="64f90-473">示例应用使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)（ASP.NET Core 应用的运行状况检查库）对 SQL Server 数据库执行运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-473">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="64f90-474">`AspNetCore.Diagnostics.HealthChecks` 对数据库执行 `SELECT 1` 查询以确认与数据库的连接是否正常。</span><span class="sxs-lookup"><span data-stu-id="64f90-474">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="64f90-475">使用查询检查数据库连接时，请选择快速返回的查询。</span><span class="sxs-lookup"><span data-stu-id="64f90-475">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="64f90-476">查询方法会面临使数据库过载和降低其性能的风险。</span><span class="sxs-lookup"><span data-stu-id="64f90-476">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="64f90-477">在大多数情况下，无需运行测试查询。</span><span class="sxs-lookup"><span data-stu-id="64f90-477">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="64f90-478">只需建立成功的数据库连接便足矣。</span><span class="sxs-lookup"><span data-stu-id="64f90-478">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="64f90-479">如果发现需要运行查询，请选择简单的 SELECT 查询，如 `SELECT 1`。</span><span class="sxs-lookup"><span data-stu-id="64f90-479">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="64f90-480">包括对 [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="64f90-480">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="64f90-481">在应用的 appsettings.json  文件中提供有效数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="64f90-481">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="64f90-482">应用使用名为 `HealthCheckSample` 的 SQL Server 数据库：</span><span class="sxs-lookup"><span data-stu-id="64f90-482">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="64f90-483">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="64f90-483">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="64f90-484">示例应用使用数据库的连接字符串 (DbHealthStartup.cs  ) 调用 `AddSqlServer` 方法：</span><span class="sxs-lookup"><span data-stu-id="64f90-484">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="64f90-485">在 `Startup.Configure` 内，在应用处理管道中调用运行状况检查中间件：</span><span class="sxs-lookup"><span data-stu-id="64f90-485">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="64f90-486">若要使用示例应用运行数据库探测方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-486">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="64f90-487">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="64f90-487">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="64f90-488">Entity Framework Core DbContext 探测</span><span class="sxs-lookup"><span data-stu-id="64f90-488">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="64f90-489">`DbContext` 检查确认应用可以与为 EF Core `DbContext` 配置的数据库通信。</span><span class="sxs-lookup"><span data-stu-id="64f90-489">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="64f90-490">满足以下条件的应用支持 `DbContext` 检查：</span><span class="sxs-lookup"><span data-stu-id="64f90-490">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="64f90-491">使用 [Entity Framework (EF) Core](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="64f90-491">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="64f90-492">包括对 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="64f90-492">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="64f90-493">`AddDbContextCheck<TContext>` 为 `DbContext` 注册运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-493">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="64f90-494">`DbContext` 作为方法的 `TContext` 提供。</span><span class="sxs-lookup"><span data-stu-id="64f90-494">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="64f90-495">重载可用于配置失败状态、标记和自定义测试查询。</span><span class="sxs-lookup"><span data-stu-id="64f90-495">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="64f90-496">默认情况下：</span><span class="sxs-lookup"><span data-stu-id="64f90-496">By default:</span></span>

* <span data-ttu-id="64f90-497">`DbContextHealthCheck` 调用 EF Core 的 `CanConnectAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="64f90-497">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="64f90-498">可以自定义在使用 `AddDbContextCheck` 方法重载检查运行状况时运行的操作。</span><span class="sxs-lookup"><span data-stu-id="64f90-498">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="64f90-499">运行状况检查的名称是 `TContext` 类型的名称。</span><span class="sxs-lookup"><span data-stu-id="64f90-499">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="64f90-500">在示例应用中，`AppDbContext` 会提供给 `AddDbContextCheck`，并在 `Startup.ConfigureServices` 中注册为服务 (DbContextHealthStartup.cs  )：</span><span class="sxs-lookup"><span data-stu-id="64f90-500">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="64f90-501">在示例应用中，`UseHealthChecks` 在 `Startup.Configure` 中添加运行状况检查中间件。</span><span class="sxs-lookup"><span data-stu-id="64f90-501">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="64f90-502">若要使用示例应用运行 `DbContext` 探测方案，请确认连接字符串指定的数据库在 SQL Server 实例中不存在。</span><span class="sxs-lookup"><span data-stu-id="64f90-502">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="64f90-503">如果该数据库存在，请删除它。</span><span class="sxs-lookup"><span data-stu-id="64f90-503">If the database exists, delete it.</span></span>

<span data-ttu-id="64f90-504">在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-504">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="64f90-505">在应用运行之后，在浏览器中对 `/health` 终结点发出请求，从而检查运行状况。</span><span class="sxs-lookup"><span data-stu-id="64f90-505">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="64f90-506">数据库和 `AppDbContext` 不存在，因此应用提供以下响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-506">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="64f90-507">触发示例应用以创建数据库。</span><span class="sxs-lookup"><span data-stu-id="64f90-507">Trigger the sample app to create the database.</span></span> <span data-ttu-id="64f90-508">向 `/createdatabase` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-508">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="64f90-509">应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-509">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="64f90-510">向 `/health` 终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-510">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="64f90-511">数据库和上下文存在，因此应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-511">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="64f90-512">触发示例应用以删除数据库。</span><span class="sxs-lookup"><span data-stu-id="64f90-512">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="64f90-513">向 `/deletedatabase` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-513">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="64f90-514">应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-514">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="64f90-515">向 `/health` 终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-515">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="64f90-516">应用提供不正常的响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-516">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="64f90-517">单独的就绪情况和运行情况探测</span><span class="sxs-lookup"><span data-stu-id="64f90-517">Separate readiness and liveness probes</span></span>

<span data-ttu-id="64f90-518">在某些托管方案中，会使用一对区分两种应用状态的运行状况检查：</span><span class="sxs-lookup"><span data-stu-id="64f90-518">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="64f90-519">应用正常运行，但尚未准备好接收请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-519">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="64f90-520">此状态是应用的就绪情况  。</span><span class="sxs-lookup"><span data-stu-id="64f90-520">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="64f90-521">应用正常运行并响应请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-521">The app is functioning and responding to requests.</span></span> <span data-ttu-id="64f90-522">此状态是应用的运行情况  。</span><span class="sxs-lookup"><span data-stu-id="64f90-522">This state is the app's *liveness*.</span></span>

<span data-ttu-id="64f90-523">就绪情况检查通常执行更广泛和耗时的检查集，以确定应用的所有子系统和资源是否都可用。</span><span class="sxs-lookup"><span data-stu-id="64f90-523">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="64f90-524">运行情况检查只是执行一个快速检查，以确定应用是否可用于处理请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-524">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="64f90-525">应用通过其就绪情况检查之后，无需使用成本高昂的就绪情况检查集来进一步增加应用负荷 &mdash; 后续检查只需检查运行情况。</span><span class="sxs-lookup"><span data-stu-id="64f90-525">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="64f90-526">示例应用包含运行状况检查，以报告[托管服务](xref:fundamentals/host/hosted-services)中长时间运行的启动任务的完成。</span><span class="sxs-lookup"><span data-stu-id="64f90-526">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="64f90-527">`StartupHostedServiceHealthCheck` 公开了属性 `StartupTaskCompleted`，托管服务在其长时间运行的任务完成时可以将该属性设置为 `true` (StartupHostedServiceHealthCheck.cs  )：</span><span class="sxs-lookup"><span data-stu-id="64f90-527">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="64f90-528">长时间运行的后台任务由[托管服务](xref:fundamentals/host/hosted-services) (Services/StartupHostedService  ) 启动。</span><span class="sxs-lookup"><span data-stu-id="64f90-528">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="64f90-529">在该任务结束时，`StartupHostedServiceHealthCheck.StartupTaskCompleted` 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="64f90-529">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="64f90-530">运行状况检查在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 与托管服务一起注册。</span><span class="sxs-lookup"><span data-stu-id="64f90-530">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="64f90-531">因为托管服务必须对运行状况检查设置该属性，所以运行状况检查也会在服务容器 (LivenessProbeStartup.cs  ) 中进行注册：</span><span class="sxs-lookup"><span data-stu-id="64f90-531">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="64f90-532">在 `Startup.Configure` 内，在应用处理管道中调用运行状况检查中间件。</span><span class="sxs-lookup"><span data-stu-id="64f90-532">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="64f90-533">在示例应用中，在 `/health/ready` 处为就绪情况检查，并且在 `/health/live` 处为运行情况检查创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="64f90-533">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="64f90-534">就绪情况检查会将运行状况检查筛选到具有 `ready` 标记的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-534">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="64f90-535">运行情况检查通过在 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) 中返回 `false` 来筛选出 `StartupHostedServiceHealthCheck`（有关详细信息，请参阅[筛选运行状况检查](#filter-health-checks)）：</span><span class="sxs-lookup"><span data-stu-id="64f90-535">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="64f90-536">若要使用示例应用运行就绪情况/运行情况配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-536">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="64f90-537">在浏览器中，访问 `/health/ready` 几次，直到过了 15 秒。</span><span class="sxs-lookup"><span data-stu-id="64f90-537">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="64f90-538">运行状况检查会在前 15 秒内报告“运行不正常”  。</span><span class="sxs-lookup"><span data-stu-id="64f90-538">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="64f90-539">15 秒之后，终结点会报告“运行正常”  ，这反映托管服务完成了长时间运行的任务。</span><span class="sxs-lookup"><span data-stu-id="64f90-539">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="64f90-540">此示例还创建了一个运行第一个就绪检查的运行状况检查发布服务器（<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现），延迟时间为两秒。</span><span class="sxs-lookup"><span data-stu-id="64f90-540">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="64f90-541">有关详细信息，请参阅[运行状况检查发布服务器](#health-check-publisher)部分。</span><span class="sxs-lookup"><span data-stu-id="64f90-541">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="64f90-542">Kubernetes 示例</span><span class="sxs-lookup"><span data-stu-id="64f90-542">Kubernetes example</span></span>

<span data-ttu-id="64f90-543">在诸如 [Kubernetes](https://kubernetes.io/) 这类环境中，使用单独的就绪情况和运行情况检查会十分有用。</span><span class="sxs-lookup"><span data-stu-id="64f90-543">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="64f90-544">在 Kubernetes 中，应用可能需要在接受请求之前执行耗时的启动工作，如基础数据库可用性测试。</span><span class="sxs-lookup"><span data-stu-id="64f90-544">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="64f90-545">使用单独检查使业务流程协调程序可以区分应用是否正常运行但尚未准备就绪，或是应用程序是否未能启动。</span><span class="sxs-lookup"><span data-stu-id="64f90-545">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="64f90-546">有关 Kubernetes 中的就绪情况和运行情况探测的详细信息，请参阅 Kubernetes 文档中的[配置运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。</span><span class="sxs-lookup"><span data-stu-id="64f90-546">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="64f90-547">以下示例演示如何使用 Kubernetes 就绪情况探测配置：</span><span class="sxs-lookup"><span data-stu-id="64f90-547">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="64f90-548">具有自定义响应编写器的基于指标的探测</span><span class="sxs-lookup"><span data-stu-id="64f90-548">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="64f90-549">示例应用演示具有自定义响应编写器的内存运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-549">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="64f90-550">如果应用使用的内存多于给定内存阈值（在示例应用中为 1 GB），则 `MemoryHealthCheck` 报告运行不正常状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-550">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="64f90-551"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 包括应用的垃圾回收器 (GC) 信息 (MemoryHealthCheck.cs  )：</span><span class="sxs-lookup"><span data-stu-id="64f90-551">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="64f90-552">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="64f90-552">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="64f90-553">`MemoryHealthCheck` 注册为服务，而不是通过将运行状况检查传递到 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 来启用它。</span><span class="sxs-lookup"><span data-stu-id="64f90-553">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="64f90-554">所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 注册服务都可供运行状况检查服务和中间件使用。</span><span class="sxs-lookup"><span data-stu-id="64f90-554">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="64f90-555">建议将运行状况检查服务注册为单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="64f90-555">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="64f90-556">在示例应用 (CustomWriterStartup.cs  ) 中：</span><span class="sxs-lookup"><span data-stu-id="64f90-556">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="64f90-557">在 `Startup.Configure` 内，在应用处理管道中调用运行状况检查中间件。</span><span class="sxs-lookup"><span data-stu-id="64f90-557">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="64f90-558">一个 `WriteResponse` 委托提供给 `ResponseWriter` 属性，以在执行运行状况检查时输出自定义 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="64f90-558">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="64f90-559">`WriteResponse` 方法将 `CompositeHealthCheckResult` 格式化为 JSON 对象，并生成运行状况检查响应的 JSON 输出：</span><span class="sxs-lookup"><span data-stu-id="64f90-559">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="64f90-560">若要使用示例应用运行具有自定义响应编写器输出的基于指标的探测，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-560">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="64f90-561">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包括基于指标的运行状况检查方案（包括磁盘存储和最大值运行情况检查）。</span><span class="sxs-lookup"><span data-stu-id="64f90-561">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="64f90-562">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="64f90-562">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="64f90-563">按端口筛选</span><span class="sxs-lookup"><span data-stu-id="64f90-563">Filter by port</span></span>

<span data-ttu-id="64f90-564">使用端口调用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 会将运行状况检查请求限制到指定端口。</span><span class="sxs-lookup"><span data-stu-id="64f90-564">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="64f90-565">这通常用于在容器环境中公开用于监视服务的端口。</span><span class="sxs-lookup"><span data-stu-id="64f90-565">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="64f90-566">示例应用使用[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)配置端口。</span><span class="sxs-lookup"><span data-stu-id="64f90-566">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="64f90-567">端口在 launchSettings.json  文件设置，并通过环境变量传递到配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="64f90-567">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="64f90-568">还必须配置服务器以在管理端口上侦听请求。</span><span class="sxs-lookup"><span data-stu-id="64f90-568">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="64f90-569">若要使用示例应用演示管理端口配置，请在 Properties  文件夹中创建 launchSettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="64f90-569">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="64f90-570">示例应用中的以下 Properties/launchSettings.json  文件未包含在示例应用的项目文件中，必须手动创建：</span><span class="sxs-lookup"><span data-stu-id="64f90-570">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="64f90-571">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="64f90-571">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="64f90-572"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 调用指定管理端口 (ManagementPortStartup.cs  )：</span><span class="sxs-lookup"><span data-stu-id="64f90-572">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="64f90-573">可以通过在代码中显式设置 URL 和管理端口，来避免在示例应用中创建 launchSettings.json  文件。</span><span class="sxs-lookup"><span data-stu-id="64f90-573">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="64f90-574">在创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 的 Program.cs  中，添加 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> 调用并提供应用的正常响应终结点和管理端口终结点。</span><span class="sxs-lookup"><span data-stu-id="64f90-574">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="64f90-575">在调用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 的 ManagementPortStartup.cs  中，显式指定管理端口。</span><span class="sxs-lookup"><span data-stu-id="64f90-575">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="64f90-576">Program.cs  :</span><span class="sxs-lookup"><span data-stu-id="64f90-576">*Program.cs*:</span></span>
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
> <span data-ttu-id="64f90-577">ManagementPortStartup.cs  ：</span><span class="sxs-lookup"><span data-stu-id="64f90-577">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="64f90-578">若要使用示例应用运行管理端口配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64f90-578">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="64f90-579">分发运行状况检查库</span><span class="sxs-lookup"><span data-stu-id="64f90-579">Distribute a health check library</span></span>

<span data-ttu-id="64f90-580">将运行状况检查作为库进行分发：</span><span class="sxs-lookup"><span data-stu-id="64f90-580">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="64f90-581">编写将 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 接口作为独立类来实现的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-581">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="64f90-582">该类可以依赖于[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)类型激活和[命名选项](xref:fundamentals/configuration/options)来访问配置数据。</span><span class="sxs-lookup"><span data-stu-id="64f90-582">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="64f90-583">在 `CheckHealthAsync` 的运行状况检查逻辑中：</span><span class="sxs-lookup"><span data-stu-id="64f90-583">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="64f90-584">方法中使用 `data1` 和 `data2` 运行探测的运行状况检查逻辑。</span><span class="sxs-lookup"><span data-stu-id="64f90-584">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="64f90-585">`AccessViolationException` 已处理。</span><span class="sxs-lookup"><span data-stu-id="64f90-585">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="64f90-586">发生 <xref:System.AccessViolationException> 时，将返回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> 和 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，以允许用户配置运行状况检查失败状态。</span><span class="sxs-lookup"><span data-stu-id="64f90-586">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

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
   ```

1. <span data-ttu-id="64f90-587">使用参数编写一个扩展方法，所使用的应用会在其 `Startup.Configure` 方法中调用它。</span><span class="sxs-lookup"><span data-stu-id="64f90-587">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="64f90-588">在以下示例中，假设以下运行状况检查方法签名：</span><span class="sxs-lookup"><span data-stu-id="64f90-588">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="64f90-589">前面的签名指示 `ExampleHealthCheck` 需要其他数据来处理运行状况检查探测逻辑。</span><span class="sxs-lookup"><span data-stu-id="64f90-589">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="64f90-590">当运行状况检查向扩展方法注册时，数据会提供给用于创建运行状况检查实例的委托。</span><span class="sxs-lookup"><span data-stu-id="64f90-590">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="64f90-591">在以下示例中，调用方会指定可选的：</span><span class="sxs-lookup"><span data-stu-id="64f90-591">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="64f90-592">运行状况检查名称 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="64f90-592">health check name (`name`).</span></span> <span data-ttu-id="64f90-593">如果为 `null`，则使用 `example_health_check`。</span><span class="sxs-lookup"><span data-stu-id="64f90-593">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="64f90-594">运行状况检查的字符串数据点 (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="64f90-594">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="64f90-595">运行状况检查的整数数据点 (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="64f90-595">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="64f90-596">如果为 `null`，则使用 `1`。</span><span class="sxs-lookup"><span data-stu-id="64f90-596">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="64f90-597">失败状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="64f90-597">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="64f90-598">默认值为 `null`。</span><span class="sxs-lookup"><span data-stu-id="64f90-598">The default is `null`.</span></span> <span data-ttu-id="64f90-599">如果为 `null`，则报告失败状态 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="64f90-599">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="64f90-600">标记 (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="64f90-600">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="64f90-601">运行状况检查发布服务器</span><span class="sxs-lookup"><span data-stu-id="64f90-601">Health Check Publisher</span></span>

<span data-ttu-id="64f90-602">当 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 添加到服务容器时，运行状况检查系统，会定期执行运行状况检查并使用结果调用 `PublishAsync`。</span><span class="sxs-lookup"><span data-stu-id="64f90-602">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="64f90-603">在期望每个进程定期调用监视系统以便确定运行状况的基于推送的运行状况监视系统方案中，这十分有用。</span><span class="sxs-lookup"><span data-stu-id="64f90-603">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="64f90-604"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 接口具有单个方法：</span><span class="sxs-lookup"><span data-stu-id="64f90-604">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="64f90-605">使用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> 可设置：</span><span class="sxs-lookup"><span data-stu-id="64f90-605"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="64f90-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; 在应用程序启动后且在应用程序执行 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例之前所应用的初始延迟。</span><span class="sxs-lookup"><span data-stu-id="64f90-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="64f90-607">延迟在启动时应用一次，不适用于后续迭代。</span><span class="sxs-lookup"><span data-stu-id="64f90-607">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="64f90-608">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="64f90-608">The default value is five seconds.</span></span>
* <span data-ttu-id="64f90-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 执行的时间。</span><span class="sxs-lookup"><span data-stu-id="64f90-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="64f90-610">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="64f90-610">The default value is 30 seconds.</span></span>
* <span data-ttu-id="64f90-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; 如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> 为 `null`（默认值），则运行状况检查发布服务器服务运行所有已注册的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="64f90-612">若要运行运行状况检查的子集，请提供用于筛选检查集的函数。</span><span class="sxs-lookup"><span data-stu-id="64f90-612">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="64f90-613">每个时间段都会评估谓词。</span><span class="sxs-lookup"><span data-stu-id="64f90-613">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="64f90-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; 执行所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例的运行状况检查的超时时间。</span><span class="sxs-lookup"><span data-stu-id="64f90-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="64f90-615">在不超时的情况下，使用 <xref:System.Threading.Timeout.InfiniteTimeSpan> 执行。</span><span class="sxs-lookup"><span data-stu-id="64f90-615">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="64f90-616">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="64f90-616">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="64f90-617">在 ASP.NET Core 2.2 版本中，<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现不支持设置 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>，它设置 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> 的值。</span><span class="sxs-lookup"><span data-stu-id="64f90-617">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="64f90-618">此问题已在 ASP.NET Core 3.0 中得到解决。</span><span class="sxs-lookup"><span data-stu-id="64f90-618">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="64f90-619">在示例应用中，`ReadinessPublisher` 是 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现。</span><span class="sxs-lookup"><span data-stu-id="64f90-619">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="64f90-620">针对每次检查，运行状况检查状态记录为：</span><span class="sxs-lookup"><span data-stu-id="64f90-620">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="64f90-621">如果运行状况检查状态为 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>，则为信息 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>)。</span><span class="sxs-lookup"><span data-stu-id="64f90-621">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="64f90-622">如果状态为 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 或 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>，则为错误 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>)。</span><span class="sxs-lookup"><span data-stu-id="64f90-622">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="64f90-623">在示例应用的 `LivenessProbeStartup` 示例中，`StartupHostedService` 就绪状态检查有两秒的启动延迟，并且每 30 秒运行一次检查。</span><span class="sxs-lookup"><span data-stu-id="64f90-623">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="64f90-624">为激活 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现，示例将 `ReadinessPublisher` 注册为[依存关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中的单一实例服务：</span><span class="sxs-lookup"><span data-stu-id="64f90-624">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="64f90-625">以下解决方法允许在已将一个或多个其他托管服务添加到应用时，向服务容器添加 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例。</span><span class="sxs-lookup"><span data-stu-id="64f90-625">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="64f90-626">ASP.NET Core 3.0 中无需此解决方法。</span><span class="sxs-lookup"><span data-stu-id="64f90-626">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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

> [!NOTE]
> <span data-ttu-id="64f90-627">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包括多个系统的发布服务器（包括 [Application Insights](/azure/application-insights/app-insights-overview)）。</span><span class="sxs-lookup"><span data-stu-id="64f90-627">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="64f90-628">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="64f90-628">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="64f90-629">使用 MapWhen 限制运行状况检查</span><span class="sxs-lookup"><span data-stu-id="64f90-629">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="64f90-630">使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 对运行状况检查终结点的请求管道进行条件分支。</span><span class="sxs-lookup"><span data-stu-id="64f90-630">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="64f90-631">在以下示例中，如果收到 `api/HealthCheck` 终结点的 GET 请求，`MapWhen` 将对请求管道进行分支以激活运行状况检查中间件：</span><span class="sxs-lookup"><span data-stu-id="64f90-631">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="64f90-632">有关详细信息，请参阅 <xref:fundamentals/middleware/index#use-run-and-map>。</span><span class="sxs-lookup"><span data-stu-id="64f90-632">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
