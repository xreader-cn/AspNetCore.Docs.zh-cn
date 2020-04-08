---
title: 在 ASP.NET Core 中使用托管服务实现后台任务
author: rick-anderson
description: 了解如何在 ASP.NET Core 中使用托管服务实现后台任务。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/host/hosted-services
ms.openlocfilehash: d3f409170eedd281fd7608c4b9835bf9443c49b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78650412"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="8b319-103">在 ASP.NET Core 中使用托管服务实现后台任务</span><span class="sxs-lookup"><span data-stu-id="8b319-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="8b319-104">作者：[Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="8b319-104">By [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b319-105">在 ASP.NET Core 中，后台任务作为托管服务实现  。</span><span class="sxs-lookup"><span data-stu-id="8b319-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="8b319-106">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="8b319-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="8b319-107">本主题提供了三个托管服务示例：</span><span class="sxs-lookup"><span data-stu-id="8b319-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="8b319-108">在计时器上运行的后台任务。</span><span class="sxs-lookup"><span data-stu-id="8b319-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="8b319-109">激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。</span><span class="sxs-lookup"><span data-stu-id="8b319-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="8b319-110">有作用域的服务可使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="8b319-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="8b319-111">按顺序运行的已排队后台任务。</span><span class="sxs-lookup"><span data-stu-id="8b319-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="8b319-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8b319-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="8b319-113">辅助角色服务模板</span><span class="sxs-lookup"><span data-stu-id="8b319-113">Worker Service template</span></span>

<span data-ttu-id="8b319-114">ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。</span><span class="sxs-lookup"><span data-stu-id="8b319-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="8b319-115">通过辅助角色服务模板创建的应用将在其项目文件中指定 Worker SDK：</span><span class="sxs-lookup"><span data-stu-id="8b319-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="8b319-116">要使用该模板作为编写托管服务应用的基础：</span><span class="sxs-lookup"><span data-stu-id="8b319-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="8b319-117">包</span><span class="sxs-lookup"><span data-stu-id="8b319-117">Package</span></span>

<span data-ttu-id="8b319-118">基于辅助角色服务模板的应用使用 `Microsoft.NET.Sdk.Worker` SDK，并且具有对 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包的显式包引用。</span><span class="sxs-lookup"><span data-stu-id="8b319-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="8b319-119">有关示例，请参阅示例应用的项目文件 (*BackgroundTasksSample.csproj*)。</span><span class="sxs-lookup"><span data-stu-id="8b319-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="8b319-120">对于使用 `Microsoft.NET.Sdk.Web` SDK 的 Web 应用，通过共享框架隐式引用 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包。</span><span class="sxs-lookup"><span data-stu-id="8b319-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="8b319-121">在应用的项目文件中不需要显式包引用。</span><span class="sxs-lookup"><span data-stu-id="8b319-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="8b319-122">IHostedService 接口</span><span class="sxs-lookup"><span data-stu-id="8b319-122">IHostedService interface</span></span>

<span data-ttu-id="8b319-123"><xref:Microsoft.Extensions.Hosting.IHostedService> 接口为主机托管的对象定义了两种方法：</span><span class="sxs-lookup"><span data-stu-id="8b319-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="8b319-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` 包含启动后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8b319-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="8b319-125">在以下操作之前调用 `StartAsync`  ：</span><span class="sxs-lookup"><span data-stu-id="8b319-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="8b319-126">已配置应用的请求处理管道 (`Startup.Configure`)。</span><span class="sxs-lookup"><span data-stu-id="8b319-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="8b319-127">已启动服务器且已触发 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)。</span><span class="sxs-lookup"><span data-stu-id="8b319-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="8b319-128">可以更改默认行为，以便在配置应用的管道并调用 `StartAsync` 之后，运行托管服务的 `ApplicationStarted`。</span><span class="sxs-lookup"><span data-stu-id="8b319-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="8b319-129">若要更改默认行为，请在调用 `VideosWatcher` 后添加托管服务（以下示例中的 `ConfigureWebHostDefaults`）：</span><span class="sxs-lookup"><span data-stu-id="8b319-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="8b319-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; 主机正常关闭时触发。</span><span class="sxs-lookup"><span data-stu-id="8b319-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="8b319-131">`StopAsync` 包含结束后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8b319-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="8b319-132">实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。</span><span class="sxs-lookup"><span data-stu-id="8b319-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="8b319-133">默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。</span><span class="sxs-lookup"><span data-stu-id="8b319-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="8b319-134">在令牌上请求取消时：</span><span class="sxs-lookup"><span data-stu-id="8b319-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="8b319-135">应中止应用正在执行的任何剩余后台操作。</span><span class="sxs-lookup"><span data-stu-id="8b319-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="8b319-136">`StopAsync` 中调用的任何方法都应及时返回。</span><span class="sxs-lookup"><span data-stu-id="8b319-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="8b319-137">但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。</span><span class="sxs-lookup"><span data-stu-id="8b319-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="8b319-138">如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="8b319-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="8b319-139">因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。</span><span class="sxs-lookup"><span data-stu-id="8b319-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="8b319-140">若要延长默认值为 5 秒的关闭超时值，请设置：</span><span class="sxs-lookup"><span data-stu-id="8b319-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="8b319-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>（当使用通用主机时）。</span><span class="sxs-lookup"><span data-stu-id="8b319-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="8b319-142">有关更多信息，请参见 <xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="8b319-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="8b319-143">使用 Web 主机时为关闭超时值主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="8b319-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="8b319-144">有关更多信息，请参见 <xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="8b319-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="8b319-145">托管服务在应用启动时激活一次，在应用关闭时正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8b319-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="8b319-146">如果在执行后台任务期间引发错误，即使未调用 `Dispose`，也应调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="8b319-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="8b319-147">BackgroundService 基类</span><span class="sxs-lookup"><span data-stu-id="8b319-147">BackgroundService base class</span></span>

<span data-ttu-id="8b319-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> 是用于实现长时间运行的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的基类。</span><span class="sxs-lookup"><span data-stu-id="8b319-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="8b319-149">调用 [ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) 来运行后台服务。</span><span class="sxs-lookup"><span data-stu-id="8b319-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="8b319-150">实现返回一个 <xref:System.Threading.Tasks.Task>，其表示后台服务的整个生存期。</span><span class="sxs-lookup"><span data-stu-id="8b319-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="8b319-151">在 [ExecuteAsync 变为异步](https://github.com/dotnet/extensions/issues/2149)（例如通过调用 `await`）之前，不会启动任何其他服务。</span><span class="sxs-lookup"><span data-stu-id="8b319-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="8b319-152">避免在 `ExecuteAsync` 中执行长时间的阻塞初始化工作。</span><span class="sxs-lookup"><span data-stu-id="8b319-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="8b319-153">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) 中的主机块等待完成 `ExecuteAsync`。</span><span class="sxs-lookup"><span data-stu-id="8b319-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="8b319-154">调用 [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) 时，将触发取消令牌。</span><span class="sxs-lookup"><span data-stu-id="8b319-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="8b319-155">当激发取消令牌以便正常关闭服务时，`ExecuteAsync` 的实现应立即完成。</span><span class="sxs-lookup"><span data-stu-id="8b319-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="8b319-156">否则，服务将在关闭超时后不正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8b319-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="8b319-157">有关更多信息，请参阅 [IHostedService interface](#ihostedservice-interface) 部分。</span><span class="sxs-lookup"><span data-stu-id="8b319-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="8b319-158">计时的后台任务</span><span class="sxs-lookup"><span data-stu-id="8b319-158">Timed background tasks</span></span>

<span data-ttu-id="8b319-159">定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。</span><span class="sxs-lookup"><span data-stu-id="8b319-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="8b319-160">计时器触发任务的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b319-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="8b319-161">在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：</span><span class="sxs-lookup"><span data-stu-id="8b319-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="8b319-162"><xref:System.Threading.Timer> 不等待先前的 `DoWork` 执行完成，因此所介绍的方法可能并不适用于所有场景。</span><span class="sxs-lookup"><span data-stu-id="8b319-162">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="8b319-163">使用 [Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) 以原子操作的形式将执行计数器递增，这可确保多个线程不会并行更新 `executionCount`。</span><span class="sxs-lookup"><span data-stu-id="8b319-163">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span>

<span data-ttu-id="8b319-164">已使用 `IHostBuilder.ConfigureServices` 扩展方法在  *(* Program.cs`AddHostedService`) 中注册该服务：</span><span class="sxs-lookup"><span data-stu-id="8b319-164">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="8b319-165">在后台任务中使用有作用域的服务</span><span class="sxs-lookup"><span data-stu-id="8b319-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="8b319-166">要在 [BackgroundService](xref:fundamentals/dependency-injection#service-lifetimes) 中使用[有作用域的服务](#backgroundservice-base-class)，请创建作用域。</span><span class="sxs-lookup"><span data-stu-id="8b319-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="8b319-167">默认情况下，不会为托管服务创建作用域。</span><span class="sxs-lookup"><span data-stu-id="8b319-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="8b319-168">作用域后台任务服务包含后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8b319-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="8b319-169">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="8b319-169">In the following example:</span></span>

* <span data-ttu-id="8b319-170">服务是异步的。</span><span class="sxs-lookup"><span data-stu-id="8b319-170">The service is asynchronous.</span></span> <span data-ttu-id="8b319-171">`DoWork` 方法返回 `Task`。</span><span class="sxs-lookup"><span data-stu-id="8b319-171">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="8b319-172">出于演示目的，在 `DoWork` 方法中等待 10 秒的延迟。</span><span class="sxs-lookup"><span data-stu-id="8b319-172">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="8b319-173"><xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中。</span><span class="sxs-lookup"><span data-stu-id="8b319-173">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="8b319-174">托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b319-174">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="8b319-175">`DoWork` 返回 `Task` 等待的 `ExecuteAsync`：</span><span class="sxs-lookup"><span data-stu-id="8b319-175">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="8b319-176">已在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="8b319-176">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="8b319-177">已使用 `AddHostedService` 扩展方法注册托管服务：</span><span class="sxs-lookup"><span data-stu-id="8b319-177">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="8b319-178">排队的后台任务</span><span class="sxs-lookup"><span data-stu-id="8b319-178">Queued background tasks</span></span>

<span data-ttu-id="8b319-179">后台任务队列基于 .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>（[暂定为 ASP.NET Core 内置版本](https://github.com/aspnet/Hosting/issues/1280)）：</span><span class="sxs-lookup"><span data-stu-id="8b319-179">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="8b319-180">在以下 `QueueHostedService` 示例中：</span><span class="sxs-lookup"><span data-stu-id="8b319-180">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="8b319-181">`BackgroundProcessing` 方法返回 `Task` 中等待的 `ExecuteAsync`。</span><span class="sxs-lookup"><span data-stu-id="8b319-181">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="8b319-182">在 `BackgroundProcessing` 中，取消排队并执行队列中的后台任务。</span><span class="sxs-lookup"><span data-stu-id="8b319-182">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="8b319-183">服务在 `StopAsync` 中停止之前，将等待工作项。</span><span class="sxs-lookup"><span data-stu-id="8b319-183">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="8b319-184">每当在输入设备上选择 `MonitorLoop` 键时，`w` 服务将处理托管服务的排队任务：</span><span class="sxs-lookup"><span data-stu-id="8b319-184">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="8b319-185">`IBackgroundTaskQueue` 注入到 `MonitorLoop` 服务中。</span><span class="sxs-lookup"><span data-stu-id="8b319-185">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="8b319-186">调用 `IBackgroundTaskQueue.QueueBackgroundWorkItem` 来将工作项排入队列。</span><span class="sxs-lookup"><span data-stu-id="8b319-186">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="8b319-187">工作项模拟长时间运行的后台任务：</span><span class="sxs-lookup"><span data-stu-id="8b319-187">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="8b319-188">将执行三次 5 秒的延迟 (`Task.Delay`)。</span><span class="sxs-lookup"><span data-stu-id="8b319-188">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="8b319-189">如果任务已取消，`try-catch` 语句将捕获 <xref:System.OperationCanceledException>。</span><span class="sxs-lookup"><span data-stu-id="8b319-189">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="8b319-190">已在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="8b319-190">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="8b319-191">已使用 `AddHostedService` 扩展方法注册托管服务：</span><span class="sxs-lookup"><span data-stu-id="8b319-191">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="8b319-192">已在 `MontiorLoop` 中启动 `Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="8b319-192">`MontiorLoop` is started in `Program.Main`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b319-193">在 ASP.NET Core 中，后台任务作为托管服务实现  。</span><span class="sxs-lookup"><span data-stu-id="8b319-193">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="8b319-194">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="8b319-194">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="8b319-195">本主题提供了三个托管服务示例：</span><span class="sxs-lookup"><span data-stu-id="8b319-195">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="8b319-196">在计时器上运行的后台任务。</span><span class="sxs-lookup"><span data-stu-id="8b319-196">Background task that runs on a timer.</span></span>
* <span data-ttu-id="8b319-197">激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。</span><span class="sxs-lookup"><span data-stu-id="8b319-197">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="8b319-198">有作用域的服务可使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="8b319-198">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="8b319-199">按顺序运行的已排队后台任务。</span><span class="sxs-lookup"><span data-stu-id="8b319-199">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="8b319-200">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8b319-200">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="8b319-201">包</span><span class="sxs-lookup"><span data-stu-id="8b319-201">Package</span></span>

<span data-ttu-id="8b319-202">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包。</span><span class="sxs-lookup"><span data-stu-id="8b319-202">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="8b319-203">IHostedService 接口</span><span class="sxs-lookup"><span data-stu-id="8b319-203">IHostedService interface</span></span>

<span data-ttu-id="8b319-204">托管服务实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口。</span><span class="sxs-lookup"><span data-stu-id="8b319-204">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="8b319-205">该接口为主机托管的对象定义了两种方法：</span><span class="sxs-lookup"><span data-stu-id="8b319-205">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="8b319-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` 包含启动后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8b319-206">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="8b319-207">当使用 [Web 主机](xref:fundamentals/host/web-host)时，会在启动服务器并触发 `StartAsync`IApplicationLifetime.ApplicationStarted[ 后调用 ](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)。</span><span class="sxs-lookup"><span data-stu-id="8b319-207">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="8b319-208">当使用[通用主机](xref:fundamentals/host/generic-host)时，会在触发 `StartAsync` 之前调用 `ApplicationStarted`。</span><span class="sxs-lookup"><span data-stu-id="8b319-208">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="8b319-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; 主机正常关闭时触发。</span><span class="sxs-lookup"><span data-stu-id="8b319-209">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="8b319-210">`StopAsync` 包含结束后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8b319-210">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="8b319-211">实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。</span><span class="sxs-lookup"><span data-stu-id="8b319-211">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="8b319-212">默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。</span><span class="sxs-lookup"><span data-stu-id="8b319-212">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="8b319-213">在令牌上请求取消时：</span><span class="sxs-lookup"><span data-stu-id="8b319-213">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="8b319-214">应中止应用正在执行的任何剩余后台操作。</span><span class="sxs-lookup"><span data-stu-id="8b319-214">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="8b319-215">`StopAsync` 中调用的任何方法都应及时返回。</span><span class="sxs-lookup"><span data-stu-id="8b319-215">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="8b319-216">但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。</span><span class="sxs-lookup"><span data-stu-id="8b319-216">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="8b319-217">如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="8b319-217">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="8b319-218">因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。</span><span class="sxs-lookup"><span data-stu-id="8b319-218">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="8b319-219">若要延长默认值为 5 秒的关闭超时值，请设置：</span><span class="sxs-lookup"><span data-stu-id="8b319-219">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="8b319-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>（当使用通用主机时）。</span><span class="sxs-lookup"><span data-stu-id="8b319-220"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="8b319-221">有关更多信息，请参见 <xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="8b319-221">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="8b319-222">使用 Web 主机时为关闭超时值主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="8b319-222">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="8b319-223">有关更多信息，请参见 <xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="8b319-223">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="8b319-224">托管服务在应用启动时激活一次，在应用关闭时正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8b319-224">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="8b319-225">如果在执行后台任务期间引发错误，即使未调用 `Dispose`，也应调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="8b319-225">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="8b319-226">计时的后台任务</span><span class="sxs-lookup"><span data-stu-id="8b319-226">Timed background tasks</span></span>

<span data-ttu-id="8b319-227">定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。</span><span class="sxs-lookup"><span data-stu-id="8b319-227">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="8b319-228">计时器触发任务的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="8b319-228">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="8b319-229">在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：</span><span class="sxs-lookup"><span data-stu-id="8b319-229">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="8b319-230"><xref:System.Threading.Timer> 不等待先前的 `DoWork` 执行完成，因此所介绍的方法可能并不适用于所有场景。</span><span class="sxs-lookup"><span data-stu-id="8b319-230">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="8b319-231">已使用 `Startup.ConfigureServices` 扩展方法在 `AddHostedService` 中注册该服务：</span><span class="sxs-lookup"><span data-stu-id="8b319-231">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="8b319-232">在后台任务中使用有作用域的服务</span><span class="sxs-lookup"><span data-stu-id="8b319-232">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="8b319-233">要在 [ 中使用](xref:fundamentals/dependency-injection#service-lifetimes)有作用域的服务`IHostedService`，请创建一个作用域。</span><span class="sxs-lookup"><span data-stu-id="8b319-233">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="8b319-234">默认情况下，不会为托管服务创建作用域。</span><span class="sxs-lookup"><span data-stu-id="8b319-234">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="8b319-235">作用域后台任务服务包含后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8b319-235">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="8b319-236">在以下示例中，将 <xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中：</span><span class="sxs-lookup"><span data-stu-id="8b319-236">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="8b319-237">托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法：</span><span class="sxs-lookup"><span data-stu-id="8b319-237">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="8b319-238">已在 `Startup.ConfigureServices` 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="8b319-238">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b319-239">已使用 `IHostedService` 扩展方法注册 `AddHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="8b319-239">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="8b319-240">排队的后台任务</span><span class="sxs-lookup"><span data-stu-id="8b319-240">Queued background tasks</span></span>

<span data-ttu-id="8b319-241">后台任务队列基于 .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>（[暂定为 ASP.NET Core 内置版本](https://github.com/aspnet/Hosting/issues/1280)）：</span><span class="sxs-lookup"><span data-stu-id="8b319-241">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="8b319-242">在 `QueueHostedService` 中，队列中的后台任务会取消排队，并作为 [BackgroundService](#backgroundservice-base-class) 执行，此类是用于实现长时间运行 `IHostedService` 的基类：</span><span class="sxs-lookup"><span data-stu-id="8b319-242">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="8b319-243">已在 `Startup.ConfigureServices` 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="8b319-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b319-244">已使用 `IHostedService` 扩展方法注册 `AddHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="8b319-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="8b319-245">在索引页模型类中：</span><span class="sxs-lookup"><span data-stu-id="8b319-245">In the Index page model class:</span></span>

* <span data-ttu-id="8b319-246">将 `IBackgroundTaskQueue` 注入构造函数并分配给 `Queue`。</span><span class="sxs-lookup"><span data-stu-id="8b319-246">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="8b319-247">注入 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 并将其分配给 `_serviceScopeFactory`。</span><span class="sxs-lookup"><span data-stu-id="8b319-247">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="8b319-248">工厂用于创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 的实例，用于在范围内创建服务。</span><span class="sxs-lookup"><span data-stu-id="8b319-248">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="8b319-249">创建范围是为了使用应用的`AppDbContext`（[设置了范围的服务](xref:fundamentals/dependency-injection#service-lifetimes)），以在 `IBackgroundTaskQueue`（单一实例服务）中写入数据库记录。</span><span class="sxs-lookup"><span data-stu-id="8b319-249">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="8b319-250">在索引页上选择“添加任务”按钮时，会执行  **方法**`OnPostAddTask`。</span><span class="sxs-lookup"><span data-stu-id="8b319-250">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="8b319-251">调用 `QueueBackgroundWorkItem` 来将工作项排入队列：</span><span class="sxs-lookup"><span data-stu-id="8b319-251">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8b319-252">其他资源</span><span class="sxs-lookup"><span data-stu-id="8b319-252">Additional resources</span></span>

* [<span data-ttu-id="8b319-253">使用 IHostedService 和 BackgroundService 类在微服务中实现后台任务</span><span class="sxs-lookup"><span data-stu-id="8b319-253">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [<span data-ttu-id="8b319-254">在 Azure 应用服务中使用 WebJobs 运行后台任务</span><span class="sxs-lookup"><span data-stu-id="8b319-254">Run background tasks with WebJobs in Azure App Service</span></span>](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
