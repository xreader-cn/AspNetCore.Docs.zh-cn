---
title: 在 ASP.NET Core 中使用托管服务实现后台任务
author: guardrex
description: 了解如何在 ASP.NET Core 中使用托管服务实现后台任务。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 0eaa3a62370c1e413840bb65f597dc664adafc38
ms.sourcegitcommit: fe88748b762525cb490f7e39089a4760f6a73a24
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71688099"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="49512-103">在 ASP.NET Core 中使用托管服务实现后台任务</span><span class="sxs-lookup"><span data-stu-id="49512-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="49512-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="49512-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="49512-105">在 ASP.NET Core 中，后台任务作为托管服务实现  。</span><span class="sxs-lookup"><span data-stu-id="49512-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="49512-106">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="49512-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="49512-107">本主题提供了三个托管服务示例：</span><span class="sxs-lookup"><span data-stu-id="49512-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="49512-108">在计时器上运行的后台任务。</span><span class="sxs-lookup"><span data-stu-id="49512-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="49512-109">激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。</span><span class="sxs-lookup"><span data-stu-id="49512-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="49512-110">有作用域的服务可使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="49512-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="49512-111">按顺序运行的已排队后台任务。</span><span class="sxs-lookup"><span data-stu-id="49512-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="49512-112">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="49512-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="49512-113">此示例应用分为两个版本：</span><span class="sxs-lookup"><span data-stu-id="49512-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="49512-114">Web 主机 &ndash; Web 主机可用于托管 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="49512-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="49512-115">本主题中所示的示例代码来自示例的 Web 主机版本。</span><span class="sxs-lookup"><span data-stu-id="49512-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="49512-116">有关详细信息，请参阅 [Web 主机](xref:fundamentals/host/web-host)主题。</span><span class="sxs-lookup"><span data-stu-id="49512-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="49512-117">通用主机 &ndash; 通用主机是 ASP.NET Core 2.1 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="49512-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="49512-118">有关详细信息，请参阅[通用主机](xref:fundamentals/host/generic-host)主题。</span><span class="sxs-lookup"><span data-stu-id="49512-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="49512-119">辅助角色服务模板</span><span class="sxs-lookup"><span data-stu-id="49512-119">Worker Service template</span></span>

<span data-ttu-id="49512-120">ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。</span><span class="sxs-lookup"><span data-stu-id="49512-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="49512-121">要使用该模板作为编写托管服务应用的基础：</span><span class="sxs-lookup"><span data-stu-id="49512-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

## <a name="package"></a><span data-ttu-id="49512-122">Package</span><span class="sxs-lookup"><span data-stu-id="49512-122">Package</span></span>

<span data-ttu-id="49512-123">对于 ASP.NET Core 应用，将隐式添加对 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="49512-123">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="49512-124">IHostedService 接口</span><span class="sxs-lookup"><span data-stu-id="49512-124">IHostedService interface</span></span>

<span data-ttu-id="49512-125"><xref:Microsoft.Extensions.Hosting.IHostedService> 接口为主机托管的对象定义了两种方法：</span><span class="sxs-lookup"><span data-stu-id="49512-125">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="49512-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` 包含启动后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="49512-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="49512-127">在以下操作之前调用 `StartAsync`  ：</span><span class="sxs-lookup"><span data-stu-id="49512-127">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="49512-128">已配置应用的请求处理管道 (`Startup.Configure`)。</span><span class="sxs-lookup"><span data-stu-id="49512-128">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="49512-129">已启动服务器且已触发 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)。</span><span class="sxs-lookup"><span data-stu-id="49512-129">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="49512-130">可以更改默认行为，以便在配置应用的管道并调用 `ApplicationStarted` 之后，运行托管服务的 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="49512-130">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="49512-131">若要更改默认行为，请在调用 `ConfigureWebHostDefaults` 后添加托管服务（以下示例中的 `VideosWatcher`）：</span><span class="sxs-lookup"><span data-stu-id="49512-131">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="49512-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; 主机正常关闭时触发。</span><span class="sxs-lookup"><span data-stu-id="49512-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="49512-133">`StopAsync` 包含结束后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="49512-133">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="49512-134">实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。</span><span class="sxs-lookup"><span data-stu-id="49512-134">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="49512-135">默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。</span><span class="sxs-lookup"><span data-stu-id="49512-135">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="49512-136">在令牌上请求取消时：</span><span class="sxs-lookup"><span data-stu-id="49512-136">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="49512-137">应中止应用正在执行的任何剩余后台操作。</span><span class="sxs-lookup"><span data-stu-id="49512-137">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="49512-138">`StopAsync` 中调用的任何方法都应及时返回。</span><span class="sxs-lookup"><span data-stu-id="49512-138">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="49512-139">但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。</span><span class="sxs-lookup"><span data-stu-id="49512-139">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="49512-140">如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="49512-140">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="49512-141">因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。</span><span class="sxs-lookup"><span data-stu-id="49512-141">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="49512-142">若要延长默认值为 5 秒的关闭超时值，请设置：</span><span class="sxs-lookup"><span data-stu-id="49512-142">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="49512-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>（当使用通用主机时）。</span><span class="sxs-lookup"><span data-stu-id="49512-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="49512-144">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="49512-144">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="49512-145">使用 Web 主机时为关闭超时值主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="49512-145">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="49512-146">有关详细信息，请参阅 <xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="49512-146">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="49512-147">托管服务在应用启动时激活一次，在应用关闭时正常关闭。</span><span class="sxs-lookup"><span data-stu-id="49512-147">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="49512-148">如果在执行后台任务期间引发错误，即使未调用 `StopAsync`，也应调用 `Dispose`。</span><span class="sxs-lookup"><span data-stu-id="49512-148">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="49512-149">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="49512-149">BackgroundService</span></span>

<span data-ttu-id="49512-150">`BackgroundService` 是用于实现长时间运行的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的基类。</span><span class="sxs-lookup"><span data-stu-id="49512-150">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="49512-151">`BackgroundService` 提供 `ExecuteAsync(CancellationToken stoppingToken)` 抽象方法来包含服务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="49512-151">`BackgroundService` provides the `ExecuteAsync(CancellationToken stoppingToken)` abstract method to contain the service's logic.</span></span> <span data-ttu-id="49512-152">当调用 [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) 时，触发 `stoppingToken`。</span><span class="sxs-lookup"><span data-stu-id="49512-152">The `stoppingToken` is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="49512-153">此方法的实现返回一个 `Task`，它表示后台服务的整个生存期。</span><span class="sxs-lookup"><span data-stu-id="49512-153">The implementation of this method returns a `Task` that represents the entire lifetime of the background service.</span></span>

<span data-ttu-id="49512-154">此外，还可以重写 `IHostedService` 上定义的方法，以便为你的服务运行启动和关闭代码  ：</span><span class="sxs-lookup"><span data-stu-id="49512-154">In addition, *optionally* override the methods defined on `IHostedService` to run startup and shutdown code for your service:</span></span>

* <span data-ttu-id="49512-155">当应用程序主机执行正常关机时，会调用 `StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="49512-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` is called when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="49512-156">当主机决定强行终止服务时，会发出 `cancellationToken` 信号。</span><span class="sxs-lookup"><span data-stu-id="49512-156">The `cancellationToken` is signalled when the host decides to forcibly terminate the service.</span></span> <span data-ttu-id="49512-157">如果重写此方法，则必须调用（和 `await`）基类方法，以确保服务正常关闭  。</span><span class="sxs-lookup"><span data-stu-id="49512-157">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service shuts down properly.</span></span>
* <span data-ttu-id="49512-158">调用 `StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` 以启动后台服务。</span><span class="sxs-lookup"><span data-stu-id="49512-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` is called to start the background service.</span></span> <span data-ttu-id="49512-159">如果启动进程中断，会发出 `cancellationToken` 信号。</span><span class="sxs-lookup"><span data-stu-id="49512-159">The `cancellationToken` is signalled if the startup process is interrupted.</span></span> <span data-ttu-id="49512-160">实现返回一个 `Task`，它表示服务的启动进程。</span><span class="sxs-lookup"><span data-stu-id="49512-160">The implementation returns a `Task` that represents the startup process for the service.</span></span> <span data-ttu-id="49512-161">在此 `Task` 完成之前，不会启动任何其他服务。</span><span class="sxs-lookup"><span data-stu-id="49512-161">No further services are started until this `Task` completes.</span></span> <span data-ttu-id="49512-162">如果重写此方法，则必须调用（和 `await`）基类方法，以确保服务正常启动  。</span><span class="sxs-lookup"><span data-stu-id="49512-162">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service starts properly.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="49512-163">计时的后台任务</span><span class="sxs-lookup"><span data-stu-id="49512-163">Timed background tasks</span></span>

<span data-ttu-id="49512-164">定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。</span><span class="sxs-lookup"><span data-stu-id="49512-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="49512-165">计时器触发任务的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="49512-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="49512-166">在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：</span><span class="sxs-lookup"><span data-stu-id="49512-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="49512-167">已使用 `AddHostedService` 扩展方法在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册该服务：</span><span class="sxs-lookup"><span data-stu-id="49512-167">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="49512-168">在后台任务中使用有作用域的服务</span><span class="sxs-lookup"><span data-stu-id="49512-168">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="49512-169">要在 `BackgroundService` 中使用[有作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)，请创建一个作用域。</span><span class="sxs-lookup"><span data-stu-id="49512-169">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="49512-170">默认情况下，不会为托管服务创建作用域。</span><span class="sxs-lookup"><span data-stu-id="49512-170">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="49512-171">作用域后台任务服务包含后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="49512-171">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="49512-172">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="49512-172">In the following example:</span></span>

* <span data-ttu-id="49512-173">服务是异步的。</span><span class="sxs-lookup"><span data-stu-id="49512-173">The service is asynchronous.</span></span> <span data-ttu-id="49512-174">`DoWork` 方法返回 `Task`。</span><span class="sxs-lookup"><span data-stu-id="49512-174">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="49512-175">出于演示目的，在 `DoWork` 方法中等待 10 秒的延迟。</span><span class="sxs-lookup"><span data-stu-id="49512-175">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="49512-176"><xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中。</span><span class="sxs-lookup"><span data-stu-id="49512-176">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="49512-177">托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="49512-177">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="49512-178">`DoWork` 返回 `ExecuteAsync` 等待的 `Task`：</span><span class="sxs-lookup"><span data-stu-id="49512-178">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="49512-179">已在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="49512-179">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="49512-180">已使用 `AddHostedService` 扩展方法注册托管服务：</span><span class="sxs-lookup"><span data-stu-id="49512-180">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="49512-181">排队的后台任务</span><span class="sxs-lookup"><span data-stu-id="49512-181">Queued background tasks</span></span>

<span data-ttu-id="49512-182">后台任务队列基于 .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>（[暂定为 ASP.NET Core 内置版本](https://github.com/aspnet/Hosting/issues/1280)）：</span><span class="sxs-lookup"><span data-stu-id="49512-182">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="49512-183">在以下 `QueueHostedService` 示例中：</span><span class="sxs-lookup"><span data-stu-id="49512-183">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="49512-184">`BackgroundProcessing` 方法返回 `ExecuteAsync` 中等待的 `Task`。</span><span class="sxs-lookup"><span data-stu-id="49512-184">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="49512-185">在 `BackgroundProcessing` 中，取消排队并执行队列中的后台任务。</span><span class="sxs-lookup"><span data-stu-id="49512-185">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28,39-40,44)]

<span data-ttu-id="49512-186">每当在输入设备上选择 `w` 键时，`MonitorLoop` 服务将处理托管服务的排队任务：</span><span class="sxs-lookup"><span data-stu-id="49512-186">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="49512-187">`IBackgroundTaskQueue` 注入到 `MonitorLoop` 服务中。</span><span class="sxs-lookup"><span data-stu-id="49512-187">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="49512-188">调用 `IBackgroundTaskQueue.QueueBackgroundWorkItem` 来将工作项排入队列。</span><span class="sxs-lookup"><span data-stu-id="49512-188">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="49512-189">已在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="49512-189">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="49512-190">已使用 `AddHostedService` 扩展方法注册托管服务：</span><span class="sxs-lookup"><span data-stu-id="49512-190">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="49512-191">在 ASP.NET Core 中，后台任务作为托管服务实现  。</span><span class="sxs-lookup"><span data-stu-id="49512-191">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="49512-192">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="49512-192">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="49512-193">本主题提供了三个托管服务示例：</span><span class="sxs-lookup"><span data-stu-id="49512-193">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="49512-194">在计时器上运行的后台任务。</span><span class="sxs-lookup"><span data-stu-id="49512-194">Background task that runs on a timer.</span></span>
* <span data-ttu-id="49512-195">激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。</span><span class="sxs-lookup"><span data-stu-id="49512-195">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="49512-196">有作用域的服务可使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="49512-196">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="49512-197">按顺序运行的已排队后台任务。</span><span class="sxs-lookup"><span data-stu-id="49512-197">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="49512-198">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="49512-198">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="49512-199">此示例应用分为两个版本：</span><span class="sxs-lookup"><span data-stu-id="49512-199">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="49512-200">Web 主机 &ndash; Web 主机可用于托管 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="49512-200">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="49512-201">本主题中所示的示例代码来自示例的 Web 主机版本。</span><span class="sxs-lookup"><span data-stu-id="49512-201">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="49512-202">有关详细信息，请参阅 [Web 主机](xref:fundamentals/host/web-host)主题。</span><span class="sxs-lookup"><span data-stu-id="49512-202">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="49512-203">通用主机 &ndash; 通用主机是 ASP.NET Core 2.1 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="49512-203">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="49512-204">有关详细信息，请参阅[通用主机](xref:fundamentals/host/generic-host)主题。</span><span class="sxs-lookup"><span data-stu-id="49512-204">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="49512-205">Package</span><span class="sxs-lookup"><span data-stu-id="49512-205">Package</span></span>

<span data-ttu-id="49512-206">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包。</span><span class="sxs-lookup"><span data-stu-id="49512-206">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="49512-207">IHostedService 接口</span><span class="sxs-lookup"><span data-stu-id="49512-207">IHostedService interface</span></span>

<span data-ttu-id="49512-208">托管服务实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口。</span><span class="sxs-lookup"><span data-stu-id="49512-208">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="49512-209">该接口为主机托管的对象定义了两种方法：</span><span class="sxs-lookup"><span data-stu-id="49512-209">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="49512-210">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` 包含启动后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="49512-210">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="49512-211">当使用 [Web 主机](xref:fundamentals/host/web-host)时，会在启动服务器并触发 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) 后调用 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="49512-211">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="49512-212">当使用[通用主机](xref:fundamentals/host/generic-host)时，会在触发 `ApplicationStarted` 之前调用 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="49512-212">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="49512-213">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; 主机正常关闭时触发。</span><span class="sxs-lookup"><span data-stu-id="49512-213">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="49512-214">`StopAsync` 包含结束后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="49512-214">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="49512-215">实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。</span><span class="sxs-lookup"><span data-stu-id="49512-215">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="49512-216">默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。</span><span class="sxs-lookup"><span data-stu-id="49512-216">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="49512-217">在令牌上请求取消时：</span><span class="sxs-lookup"><span data-stu-id="49512-217">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="49512-218">应中止应用正在执行的任何剩余后台操作。</span><span class="sxs-lookup"><span data-stu-id="49512-218">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="49512-219">`StopAsync` 中调用的任何方法都应及时返回。</span><span class="sxs-lookup"><span data-stu-id="49512-219">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="49512-220">但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。</span><span class="sxs-lookup"><span data-stu-id="49512-220">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="49512-221">如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="49512-221">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="49512-222">因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。</span><span class="sxs-lookup"><span data-stu-id="49512-222">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="49512-223">若要延长默认值为 5 秒的关闭超时值，请设置：</span><span class="sxs-lookup"><span data-stu-id="49512-223">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="49512-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>（当使用通用主机时）。</span><span class="sxs-lookup"><span data-stu-id="49512-224"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="49512-225">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="49512-225">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="49512-226">使用 Web 主机时为关闭超时值主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="49512-226">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="49512-227">有关详细信息，请参阅 <xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="49512-227">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="49512-228">托管服务在应用启动时激活一次，在应用关闭时正常关闭。</span><span class="sxs-lookup"><span data-stu-id="49512-228">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="49512-229">如果在执行后台任务期间引发错误，即使未调用 `StopAsync`，也应调用 `Dispose`。</span><span class="sxs-lookup"><span data-stu-id="49512-229">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="49512-230">计时的后台任务</span><span class="sxs-lookup"><span data-stu-id="49512-230">Timed background tasks</span></span>

<span data-ttu-id="49512-231">定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。</span><span class="sxs-lookup"><span data-stu-id="49512-231">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="49512-232">计时器触发任务的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="49512-232">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="49512-233">在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：</span><span class="sxs-lookup"><span data-stu-id="49512-233">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="49512-234">已使用 `AddHostedService` 扩展方法在 `Startup.ConfigureServices` 中注册该服务：</span><span class="sxs-lookup"><span data-stu-id="49512-234">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="49512-235">在后台任务中使用有作用域的服务</span><span class="sxs-lookup"><span data-stu-id="49512-235">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="49512-236">要在 `IHostedService` 中使用[有作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)，请创建一个作用域。</span><span class="sxs-lookup"><span data-stu-id="49512-236">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="49512-237">默认情况下，不会为托管服务创建作用域。</span><span class="sxs-lookup"><span data-stu-id="49512-237">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="49512-238">作用域后台任务服务包含后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="49512-238">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="49512-239">在以下示例中，将 <xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中：</span><span class="sxs-lookup"><span data-stu-id="49512-239">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="49512-240">托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法：</span><span class="sxs-lookup"><span data-stu-id="49512-240">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="49512-241">已在 `Startup.ConfigureServices` 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="49512-241">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="49512-242">已使用 `AddHostedService` 扩展方法注册 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="49512-242">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="49512-243">排队的后台任务</span><span class="sxs-lookup"><span data-stu-id="49512-243">Queued background tasks</span></span>

<span data-ttu-id="49512-244">后台任务队列基于 .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>（[暂定为 ASP.NET Core 内置版本](https://github.com/aspnet/Hosting/issues/1280)）：</span><span class="sxs-lookup"><span data-stu-id="49512-244">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="49512-245">在 `QueueHostedService` 中，队列中的后台任务会取消排队，并作为 <xref:Microsoft.Extensions.Hosting.BackgroundService> 执行，此类是用于实现长时间运行 `IHostedService` 的基类：</span><span class="sxs-lookup"><span data-stu-id="49512-245">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="49512-246">已在 `Startup.ConfigureServices` 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="49512-246">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="49512-247">已使用 `AddHostedService` 扩展方法注册 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="49512-247">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="49512-248">在索引页模型类中：</span><span class="sxs-lookup"><span data-stu-id="49512-248">In the Index page model class:</span></span>

* <span data-ttu-id="49512-249">将 `IBackgroundTaskQueue` 注入构造函数并分配给 `Queue`。</span><span class="sxs-lookup"><span data-stu-id="49512-249">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="49512-250">注入 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 并将其分配给 `_serviceScopeFactory`。</span><span class="sxs-lookup"><span data-stu-id="49512-250">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="49512-251">工厂用于创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 的实例，用于在范围内创建服务。</span><span class="sxs-lookup"><span data-stu-id="49512-251">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="49512-252">创建范围是为了使用应用的`AppDbContext`（[设置了范围的服务](xref:fundamentals/dependency-injection#service-lifetimes)），以在 `IBackgroundTaskQueue`（单一实例服务）中写入数据库记录。</span><span class="sxs-lookup"><span data-stu-id="49512-252">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="49512-253">在索引页上选择“添加任务”按钮时，会执行 `OnPostAddTask` 方法  。</span><span class="sxs-lookup"><span data-stu-id="49512-253">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="49512-254">调用 `QueueBackgroundWorkItem` 来将工作项排入队列：</span><span class="sxs-lookup"><span data-stu-id="49512-254">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="49512-255">其他资源</span><span class="sxs-lookup"><span data-stu-id="49512-255">Additional resources</span></span>

* [<span data-ttu-id="49512-256">使用 IHostedService 和 BackgroundService 类在微服务中实现后台任务</span><span class="sxs-lookup"><span data-stu-id="49512-256">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
