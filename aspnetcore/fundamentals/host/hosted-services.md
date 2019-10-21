---
title: 在 ASP.NET Core 中使用托管服务实现后台任务
author: guardrex
description: 了解如何在 ASP.NET Core 中使用托管服务实现后台任务。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c1fbb5ae8ffc4ee506f42df6a4cbbe845b2b903d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333656"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="aff80-103">在 ASP.NET Core 中使用托管服务实现后台任务</span><span class="sxs-lookup"><span data-stu-id="aff80-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="aff80-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="aff80-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aff80-105">在 ASP.NET Core 中，后台任务作为托管服务实现  。</span><span class="sxs-lookup"><span data-stu-id="aff80-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="aff80-106">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="aff80-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="aff80-107">本主题提供了三个托管服务示例：</span><span class="sxs-lookup"><span data-stu-id="aff80-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="aff80-108">在计时器上运行的后台任务。</span><span class="sxs-lookup"><span data-stu-id="aff80-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="aff80-109">激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。</span><span class="sxs-lookup"><span data-stu-id="aff80-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="aff80-110">有作用域的服务可使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="aff80-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="aff80-111">按顺序运行的已排队后台任务。</span><span class="sxs-lookup"><span data-stu-id="aff80-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="aff80-112">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="aff80-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="aff80-113">此示例应用分为两个版本：</span><span class="sxs-lookup"><span data-stu-id="aff80-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="aff80-114">Web 主机 &ndash; Web 主机可用于托管 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="aff80-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="aff80-115">本主题中所示的示例代码来自示例的 Web 主机版本。</span><span class="sxs-lookup"><span data-stu-id="aff80-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="aff80-116">有关详细信息，请参阅 [Web 主机](xref:fundamentals/host/web-host)主题。</span><span class="sxs-lookup"><span data-stu-id="aff80-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="aff80-117">通用主机 &ndash; 通用主机是 ASP.NET Core 2.1 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="aff80-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="aff80-118">有关详细信息，请参阅[通用主机](xref:fundamentals/host/generic-host)主题。</span><span class="sxs-lookup"><span data-stu-id="aff80-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="aff80-119">辅助角色服务模板</span><span class="sxs-lookup"><span data-stu-id="aff80-119">Worker Service template</span></span>

<span data-ttu-id="aff80-120">ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。</span><span class="sxs-lookup"><span data-stu-id="aff80-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="aff80-121">要使用该模板作为编写托管服务应用的基础：</span><span class="sxs-lookup"><span data-stu-id="aff80-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

## <a name="package"></a><span data-ttu-id="aff80-122">Package</span><span class="sxs-lookup"><span data-stu-id="aff80-122">Package</span></span>

<span data-ttu-id="aff80-123">对于 ASP.NET Core 应用，将隐式添加对 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="aff80-123">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="aff80-124">IHostedService 接口</span><span class="sxs-lookup"><span data-stu-id="aff80-124">IHostedService interface</span></span>

<span data-ttu-id="aff80-125"><xref:Microsoft.Extensions.Hosting.IHostedService> 接口为主机托管的对象定义了两种方法：</span><span class="sxs-lookup"><span data-stu-id="aff80-125">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="aff80-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` 包含启动后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="aff80-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="aff80-127">在以下操作之前调用 `StartAsync`  ：</span><span class="sxs-lookup"><span data-stu-id="aff80-127">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="aff80-128">已配置应用的请求处理管道 (`Startup.Configure`)。</span><span class="sxs-lookup"><span data-stu-id="aff80-128">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="aff80-129">已启动服务器且已触发 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)。</span><span class="sxs-lookup"><span data-stu-id="aff80-129">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="aff80-130">可以更改默认行为，以便在配置应用的管道并调用 `ApplicationStarted` 之后，运行托管服务的 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="aff80-130">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="aff80-131">若要更改默认行为，请在调用 `ConfigureWebHostDefaults` 后添加托管服务（以下示例中的 `VideosWatcher`）：</span><span class="sxs-lookup"><span data-stu-id="aff80-131">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

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
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* <span data-ttu-id="aff80-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; 主机正常关闭时触发。</span><span class="sxs-lookup"><span data-stu-id="aff80-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="aff80-133">`StopAsync` 包含结束后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="aff80-133">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="aff80-134">实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。</span><span class="sxs-lookup"><span data-stu-id="aff80-134">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="aff80-135">默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。</span><span class="sxs-lookup"><span data-stu-id="aff80-135">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="aff80-136">在令牌上请求取消时：</span><span class="sxs-lookup"><span data-stu-id="aff80-136">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="aff80-137">应中止应用正在执行的任何剩余后台操作。</span><span class="sxs-lookup"><span data-stu-id="aff80-137">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="aff80-138">`StopAsync` 中调用的任何方法都应及时返回。</span><span class="sxs-lookup"><span data-stu-id="aff80-138">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="aff80-139">但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。</span><span class="sxs-lookup"><span data-stu-id="aff80-139">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="aff80-140">如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="aff80-140">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="aff80-141">因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。</span><span class="sxs-lookup"><span data-stu-id="aff80-141">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="aff80-142">若要延长默认值为 5 秒的关闭超时值，请设置：</span><span class="sxs-lookup"><span data-stu-id="aff80-142">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="aff80-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>（当使用通用主机时）。</span><span class="sxs-lookup"><span data-stu-id="aff80-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="aff80-144">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="aff80-144">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="aff80-145">使用 Web 主机时为关闭超时值主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="aff80-145">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="aff80-146">有关详细信息，请参阅 <xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="aff80-146">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="aff80-147">托管服务在应用启动时激活一次，在应用关闭时正常关闭。</span><span class="sxs-lookup"><span data-stu-id="aff80-147">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="aff80-148">如果在执行后台任务期间引发错误，即使未调用 `StopAsync`，也应调用 `Dispose`。</span><span class="sxs-lookup"><span data-stu-id="aff80-148">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="aff80-149">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="aff80-149">BackgroundService</span></span>

<span data-ttu-id="aff80-150">`BackgroundService` 是用于实现长时间运行的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的基类。</span><span class="sxs-lookup"><span data-stu-id="aff80-150">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="aff80-151">`BackgroundService` 提供 `ExecuteAsync(CancellationToken stoppingToken)` 抽象方法来包含服务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="aff80-151">`BackgroundService` provides the `ExecuteAsync(CancellationToken stoppingToken)` abstract method to contain the service's logic.</span></span> <span data-ttu-id="aff80-152">当调用 [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) 时，触发 `stoppingToken`。</span><span class="sxs-lookup"><span data-stu-id="aff80-152">The `stoppingToken` is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="aff80-153">此方法的实现返回一个 `Task`，它表示后台服务的整个生存期。</span><span class="sxs-lookup"><span data-stu-id="aff80-153">The implementation of this method returns a `Task` that represents the entire lifetime of the background service.</span></span>

<span data-ttu-id="aff80-154">此外，还可以重写 `IHostedService` 上定义的方法，以便为你的服务运行启动和关闭代码  ：</span><span class="sxs-lookup"><span data-stu-id="aff80-154">In addition, *optionally* override the methods defined on `IHostedService` to run startup and shutdown code for your service:</span></span>

* <span data-ttu-id="aff80-155">当应用程序主机执行正常关机时，会调用 `StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="aff80-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` is called when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="aff80-156">当主机决定强行终止服务时，会发出 `cancellationToken` 信号。</span><span class="sxs-lookup"><span data-stu-id="aff80-156">The `cancellationToken` is signalled when the host decides to forcibly terminate the service.</span></span> <span data-ttu-id="aff80-157">如果重写此方法，则必须调用（和 `await`）基类方法，以确保服务正常关闭  。</span><span class="sxs-lookup"><span data-stu-id="aff80-157">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service shuts down properly.</span></span>
* <span data-ttu-id="aff80-158">调用 `StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` 以启动后台服务。</span><span class="sxs-lookup"><span data-stu-id="aff80-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` is called to start the background service.</span></span> <span data-ttu-id="aff80-159">如果启动进程中断，会发出 `cancellationToken` 信号。</span><span class="sxs-lookup"><span data-stu-id="aff80-159">The `cancellationToken` is signalled if the startup process is interrupted.</span></span> <span data-ttu-id="aff80-160">实现返回一个 `Task`，它表示服务的启动进程。</span><span class="sxs-lookup"><span data-stu-id="aff80-160">The implementation returns a `Task` that represents the startup process for the service.</span></span> <span data-ttu-id="aff80-161">在此 `Task` 完成之前，不会启动任何其他服务。</span><span class="sxs-lookup"><span data-stu-id="aff80-161">No further services are started until this `Task` completes.</span></span> <span data-ttu-id="aff80-162">如果重写此方法，则必须调用（和 `await`）基类方法，以确保服务正常启动  。</span><span class="sxs-lookup"><span data-stu-id="aff80-162">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service starts properly.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="aff80-163">计时的后台任务</span><span class="sxs-lookup"><span data-stu-id="aff80-163">Timed background tasks</span></span>

<span data-ttu-id="aff80-164">定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。</span><span class="sxs-lookup"><span data-stu-id="aff80-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="aff80-165">计时器触发任务的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="aff80-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="aff80-166">在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：</span><span class="sxs-lookup"><span data-stu-id="aff80-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="aff80-167">已使用 `AddHostedService` 扩展方法在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册该服务：</span><span class="sxs-lookup"><span data-stu-id="aff80-167">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="aff80-168">在后台任务中使用有作用域的服务</span><span class="sxs-lookup"><span data-stu-id="aff80-168">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="aff80-169">要在 `BackgroundService` 中使用[有作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)，请创建一个作用域。</span><span class="sxs-lookup"><span data-stu-id="aff80-169">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="aff80-170">默认情况下，不会为托管服务创建作用域。</span><span class="sxs-lookup"><span data-stu-id="aff80-170">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="aff80-171">作用域后台任务服务包含后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="aff80-171">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="aff80-172">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="aff80-172">In the following example:</span></span>

* <span data-ttu-id="aff80-173">服务是异步的。</span><span class="sxs-lookup"><span data-stu-id="aff80-173">The service is asynchronous.</span></span> <span data-ttu-id="aff80-174">`DoWork` 方法返回 `Task`。</span><span class="sxs-lookup"><span data-stu-id="aff80-174">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="aff80-175">出于演示目的，在 `DoWork` 方法中等待 10 秒的延迟。</span><span class="sxs-lookup"><span data-stu-id="aff80-175">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="aff80-176"><xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中。</span><span class="sxs-lookup"><span data-stu-id="aff80-176">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="aff80-177">托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="aff80-177">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="aff80-178">`DoWork` 返回 `ExecuteAsync` 等待的 `Task`：</span><span class="sxs-lookup"><span data-stu-id="aff80-178">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="aff80-179">已在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="aff80-179">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="aff80-180">已使用 `AddHostedService` 扩展方法注册托管服务：</span><span class="sxs-lookup"><span data-stu-id="aff80-180">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="aff80-181">排队的后台任务</span><span class="sxs-lookup"><span data-stu-id="aff80-181">Queued background tasks</span></span>

<span data-ttu-id="aff80-182">后台任务队列基于 .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>（[暂定为 ASP.NET Core 内置版本](https://github.com/aspnet/Hosting/issues/1280)）：</span><span class="sxs-lookup"><span data-stu-id="aff80-182">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="aff80-183">在以下 `QueueHostedService` 示例中：</span><span class="sxs-lookup"><span data-stu-id="aff80-183">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="aff80-184">`BackgroundProcessing` 方法返回 `ExecuteAsync` 中等待的 `Task`。</span><span class="sxs-lookup"><span data-stu-id="aff80-184">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="aff80-185">在 `BackgroundProcessing` 中，取消排队并执行队列中的后台任务。</span><span class="sxs-lookup"><span data-stu-id="aff80-185">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="aff80-186">服务在 `StopAsync` 中停止之前，将等待工作项。</span><span class="sxs-lookup"><span data-stu-id="aff80-186">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="aff80-187">每当在输入设备上选择 `w` 键时，`MonitorLoop` 服务将处理托管服务的排队任务：</span><span class="sxs-lookup"><span data-stu-id="aff80-187">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="aff80-188">`IBackgroundTaskQueue` 注入到 `MonitorLoop` 服务中。</span><span class="sxs-lookup"><span data-stu-id="aff80-188">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="aff80-189">调用 `IBackgroundTaskQueue.QueueBackgroundWorkItem` 来将工作项排入队列。</span><span class="sxs-lookup"><span data-stu-id="aff80-189">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="aff80-190">工作项模拟长时间运行的后台任务：</span><span class="sxs-lookup"><span data-stu-id="aff80-190">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="aff80-191">将执行三次 5 秒的延迟 (`Task.Delay`)。</span><span class="sxs-lookup"><span data-stu-id="aff80-191">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="aff80-192">如果任务已取消，`try-catch` 语句将捕获 <xref:System.OperationCanceledException>。</span><span class="sxs-lookup"><span data-stu-id="aff80-192">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="aff80-193">已在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="aff80-193">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="aff80-194">已使用 `AddHostedService` 扩展方法注册托管服务：</span><span class="sxs-lookup"><span data-stu-id="aff80-194">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aff80-195">在 ASP.NET Core 中，后台任务作为托管服务实现  。</span><span class="sxs-lookup"><span data-stu-id="aff80-195">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="aff80-196">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="aff80-196">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="aff80-197">本主题提供了三个托管服务示例：</span><span class="sxs-lookup"><span data-stu-id="aff80-197">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="aff80-198">在计时器上运行的后台任务。</span><span class="sxs-lookup"><span data-stu-id="aff80-198">Background task that runs on a timer.</span></span>
* <span data-ttu-id="aff80-199">激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。</span><span class="sxs-lookup"><span data-stu-id="aff80-199">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="aff80-200">有作用域的服务可使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="aff80-200">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="aff80-201">按顺序运行的已排队后台任务。</span><span class="sxs-lookup"><span data-stu-id="aff80-201">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="aff80-202">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="aff80-202">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="aff80-203">此示例应用分为两个版本：</span><span class="sxs-lookup"><span data-stu-id="aff80-203">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="aff80-204">Web 主机 &ndash; Web 主机可用于托管 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="aff80-204">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="aff80-205">本主题中所示的示例代码来自示例的 Web 主机版本。</span><span class="sxs-lookup"><span data-stu-id="aff80-205">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="aff80-206">有关详细信息，请参阅 [Web 主机](xref:fundamentals/host/web-host)主题。</span><span class="sxs-lookup"><span data-stu-id="aff80-206">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="aff80-207">通用主机 &ndash; 通用主机是 ASP.NET Core 2.1 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="aff80-207">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="aff80-208">有关详细信息，请参阅[通用主机](xref:fundamentals/host/generic-host)主题。</span><span class="sxs-lookup"><span data-stu-id="aff80-208">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="aff80-209">Package</span><span class="sxs-lookup"><span data-stu-id="aff80-209">Package</span></span>

<span data-ttu-id="aff80-210">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包。</span><span class="sxs-lookup"><span data-stu-id="aff80-210">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="aff80-211">IHostedService 接口</span><span class="sxs-lookup"><span data-stu-id="aff80-211">IHostedService interface</span></span>

<span data-ttu-id="aff80-212">托管服务实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口。</span><span class="sxs-lookup"><span data-stu-id="aff80-212">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="aff80-213">该接口为主机托管的对象定义了两种方法：</span><span class="sxs-lookup"><span data-stu-id="aff80-213">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="aff80-214">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` 包含启动后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="aff80-214">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="aff80-215">当使用 [Web 主机](xref:fundamentals/host/web-host)时，会在启动服务器并触发 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) 后调用 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="aff80-215">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="aff80-216">当使用[通用主机](xref:fundamentals/host/generic-host)时，会在触发 `ApplicationStarted` 之前调用 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="aff80-216">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="aff80-217">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; 主机正常关闭时触发。</span><span class="sxs-lookup"><span data-stu-id="aff80-217">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="aff80-218">`StopAsync` 包含结束后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="aff80-218">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="aff80-219">实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。</span><span class="sxs-lookup"><span data-stu-id="aff80-219">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="aff80-220">默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。</span><span class="sxs-lookup"><span data-stu-id="aff80-220">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="aff80-221">在令牌上请求取消时：</span><span class="sxs-lookup"><span data-stu-id="aff80-221">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="aff80-222">应中止应用正在执行的任何剩余后台操作。</span><span class="sxs-lookup"><span data-stu-id="aff80-222">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="aff80-223">`StopAsync` 中调用的任何方法都应及时返回。</span><span class="sxs-lookup"><span data-stu-id="aff80-223">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="aff80-224">但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。</span><span class="sxs-lookup"><span data-stu-id="aff80-224">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="aff80-225">如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="aff80-225">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="aff80-226">因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。</span><span class="sxs-lookup"><span data-stu-id="aff80-226">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="aff80-227">若要延长默认值为 5 秒的关闭超时值，请设置：</span><span class="sxs-lookup"><span data-stu-id="aff80-227">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="aff80-228"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>（当使用通用主机时）。</span><span class="sxs-lookup"><span data-stu-id="aff80-228"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="aff80-229">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="aff80-229">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="aff80-230">使用 Web 主机时为关闭超时值主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="aff80-230">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="aff80-231">有关详细信息，请参阅 <xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="aff80-231">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="aff80-232">托管服务在应用启动时激活一次，在应用关闭时正常关闭。</span><span class="sxs-lookup"><span data-stu-id="aff80-232">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="aff80-233">如果在执行后台任务期间引发错误，即使未调用 `StopAsync`，也应调用 `Dispose`。</span><span class="sxs-lookup"><span data-stu-id="aff80-233">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="aff80-234">计时的后台任务</span><span class="sxs-lookup"><span data-stu-id="aff80-234">Timed background tasks</span></span>

<span data-ttu-id="aff80-235">定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。</span><span class="sxs-lookup"><span data-stu-id="aff80-235">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="aff80-236">计时器触发任务的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="aff80-236">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="aff80-237">在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：</span><span class="sxs-lookup"><span data-stu-id="aff80-237">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="aff80-238">已使用 `AddHostedService` 扩展方法在 `Startup.ConfigureServices` 中注册该服务：</span><span class="sxs-lookup"><span data-stu-id="aff80-238">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="aff80-239">在后台任务中使用有作用域的服务</span><span class="sxs-lookup"><span data-stu-id="aff80-239">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="aff80-240">要在 `IHostedService` 中使用[有作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)，请创建一个作用域。</span><span class="sxs-lookup"><span data-stu-id="aff80-240">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="aff80-241">默认情况下，不会为托管服务创建作用域。</span><span class="sxs-lookup"><span data-stu-id="aff80-241">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="aff80-242">作用域后台任务服务包含后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="aff80-242">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="aff80-243">在以下示例中，将 <xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中：</span><span class="sxs-lookup"><span data-stu-id="aff80-243">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="aff80-244">托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法：</span><span class="sxs-lookup"><span data-stu-id="aff80-244">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="aff80-245">已在 `Startup.ConfigureServices` 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="aff80-245">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aff80-246">已使用 `AddHostedService` 扩展方法注册 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="aff80-246">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="aff80-247">排队的后台任务</span><span class="sxs-lookup"><span data-stu-id="aff80-247">Queued background tasks</span></span>

<span data-ttu-id="aff80-248">后台任务队列基于 .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>（[暂定为 ASP.NET Core 内置版本](https://github.com/aspnet/Hosting/issues/1280)）：</span><span class="sxs-lookup"><span data-stu-id="aff80-248">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="aff80-249">在 `QueueHostedService` 中，队列中的后台任务会取消排队，并作为 <xref:Microsoft.Extensions.Hosting.BackgroundService> 执行，此类是用于实现长时间运行 `IHostedService` 的基类：</span><span class="sxs-lookup"><span data-stu-id="aff80-249">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="aff80-250">已在 `Startup.ConfigureServices` 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="aff80-250">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="aff80-251">已使用 `AddHostedService` 扩展方法注册 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="aff80-251">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="aff80-252">在索引页模型类中：</span><span class="sxs-lookup"><span data-stu-id="aff80-252">In the Index page model class:</span></span>

* <span data-ttu-id="aff80-253">将 `IBackgroundTaskQueue` 注入构造函数并分配给 `Queue`。</span><span class="sxs-lookup"><span data-stu-id="aff80-253">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="aff80-254">注入 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 并将其分配给 `_serviceScopeFactory`。</span><span class="sxs-lookup"><span data-stu-id="aff80-254">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="aff80-255">工厂用于创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 的实例，用于在范围内创建服务。</span><span class="sxs-lookup"><span data-stu-id="aff80-255">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="aff80-256">创建范围是为了使用应用的`AppDbContext`（[设置了范围的服务](xref:fundamentals/dependency-injection#service-lifetimes)），以在 `IBackgroundTaskQueue`（单一实例服务）中写入数据库记录。</span><span class="sxs-lookup"><span data-stu-id="aff80-256">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="aff80-257">在索引页上选择“添加任务”按钮时，会执行 `OnPostAddTask` 方法  。</span><span class="sxs-lookup"><span data-stu-id="aff80-257">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="aff80-258">调用 `QueueBackgroundWorkItem` 来将工作项排入队列：</span><span class="sxs-lookup"><span data-stu-id="aff80-258">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="aff80-259">其他资源</span><span class="sxs-lookup"><span data-stu-id="aff80-259">Additional resources</span></span>

* [<span data-ttu-id="aff80-260">使用 IHostedService 和 BackgroundService 类在微服务中实现后台任务</span><span class="sxs-lookup"><span data-stu-id="aff80-260">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
