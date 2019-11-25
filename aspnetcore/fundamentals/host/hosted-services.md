---
title: 在 ASP.NET Core 中使用托管服务实现后台任务
author: guardrex
description: 了解如何在 ASP.NET Core 中使用托管服务实现后台任务。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/19/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: da3c2679005714a3d82de94cf3bc3c809aa3500d
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239724"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="f7273-103">在 ASP.NET Core 中使用托管服务实现后台任务</span><span class="sxs-lookup"><span data-stu-id="f7273-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="f7273-104">作者：[Luke Latham](https://github.com/guardrex) 和 [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="f7273-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f7273-105">在 ASP.NET Core 中，后台任务作为托管服务实现  。</span><span class="sxs-lookup"><span data-stu-id="f7273-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="f7273-106">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="f7273-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="f7273-107">本主题提供了三个托管服务示例：</span><span class="sxs-lookup"><span data-stu-id="f7273-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="f7273-108">在计时器上运行的后台任务。</span><span class="sxs-lookup"><span data-stu-id="f7273-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="f7273-109">激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。</span><span class="sxs-lookup"><span data-stu-id="f7273-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="f7273-110">有作用域的服务可使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f7273-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="f7273-111">按顺序运行的已排队后台任务。</span><span class="sxs-lookup"><span data-stu-id="f7273-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="f7273-112">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f7273-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="f7273-113">辅助角色服务模板</span><span class="sxs-lookup"><span data-stu-id="f7273-113">Worker Service template</span></span>

<span data-ttu-id="f7273-114">ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。</span><span class="sxs-lookup"><span data-stu-id="f7273-114">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="f7273-115">通过辅助角色服务模板创建的应用将在其项目文件中指定 Worker SDK：</span><span class="sxs-lookup"><span data-stu-id="f7273-115">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="f7273-116">要使用该模板作为编写托管服务应用的基础：</span><span class="sxs-lookup"><span data-stu-id="f7273-116">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="f7273-117">Package</span><span class="sxs-lookup"><span data-stu-id="f7273-117">Package</span></span>

<span data-ttu-id="f7273-118">基于辅助角色服务模板的应用使用 `Microsoft.NET.Sdk.Worker` SDK，并且具有对 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包的显式包引用。</span><span class="sxs-lookup"><span data-stu-id="f7273-118">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="f7273-119">有关示例，请参阅示例应用的项目文件 (*BackgroundTasksSample.csproj*)。</span><span class="sxs-lookup"><span data-stu-id="f7273-119">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span>

<span data-ttu-id="f7273-120">对于使用 `Microsoft.NET.Sdk.Web` SDK 的 Web 应用，通过共享框架隐式引用 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包。</span><span class="sxs-lookup"><span data-stu-id="f7273-120">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span> <span data-ttu-id="f7273-121">在应用的项目文件中不需要显式包引用。</span><span class="sxs-lookup"><span data-stu-id="f7273-121">An explicit package reference in the app's project file isn't required.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="f7273-122">IHostedService 接口</span><span class="sxs-lookup"><span data-stu-id="f7273-122">IHostedService interface</span></span>

<span data-ttu-id="f7273-123"><xref:Microsoft.Extensions.Hosting.IHostedService> 接口为主机托管的对象定义了两种方法：</span><span class="sxs-lookup"><span data-stu-id="f7273-123">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="f7273-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` 包含启动后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f7273-124">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="f7273-125">在以下操作之前调用 `StartAsync`  ：</span><span class="sxs-lookup"><span data-stu-id="f7273-125">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="f7273-126">已配置应用的请求处理管道 (`Startup.Configure`)。</span><span class="sxs-lookup"><span data-stu-id="f7273-126">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="f7273-127">已启动服务器且已触发 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)。</span><span class="sxs-lookup"><span data-stu-id="f7273-127">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="f7273-128">可以更改默认行为，以便在配置应用的管道并调用 `ApplicationStarted` 之后，运行托管服务的 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="f7273-128">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="f7273-129">若要更改默认行为，请在调用 `ConfigureWebHostDefaults` 后添加托管服务（以下示例中的 `VideosWatcher`）：</span><span class="sxs-lookup"><span data-stu-id="f7273-129">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="f7273-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; 主机正常关闭时触发。</span><span class="sxs-lookup"><span data-stu-id="f7273-130">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="f7273-131">`StopAsync` 包含结束后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f7273-131">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="f7273-132">实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。</span><span class="sxs-lookup"><span data-stu-id="f7273-132">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="f7273-133">默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。</span><span class="sxs-lookup"><span data-stu-id="f7273-133">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="f7273-134">在令牌上请求取消时：</span><span class="sxs-lookup"><span data-stu-id="f7273-134">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="f7273-135">应中止应用正在执行的任何剩余后台操作。</span><span class="sxs-lookup"><span data-stu-id="f7273-135">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="f7273-136">`StopAsync` 中调用的任何方法都应及时返回。</span><span class="sxs-lookup"><span data-stu-id="f7273-136">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="f7273-137">但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。</span><span class="sxs-lookup"><span data-stu-id="f7273-137">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="f7273-138">如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="f7273-138">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="f7273-139">因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。</span><span class="sxs-lookup"><span data-stu-id="f7273-139">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="f7273-140">若要延长默认值为 5 秒的关闭超时值，请设置：</span><span class="sxs-lookup"><span data-stu-id="f7273-140">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="f7273-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>（当使用通用主机时）。</span><span class="sxs-lookup"><span data-stu-id="f7273-141"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="f7273-142">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="f7273-142">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="f7273-143">使用 Web 主机时为关闭超时值主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="f7273-143">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="f7273-144">有关详细信息，请参阅 <xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="f7273-144">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="f7273-145">托管服务在应用启动时激活一次，在应用关闭时正常关闭。</span><span class="sxs-lookup"><span data-stu-id="f7273-145">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="f7273-146">如果在执行后台任务期间引发错误，即使未调用 `StopAsync`，也应调用 `Dispose`。</span><span class="sxs-lookup"><span data-stu-id="f7273-146">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="f7273-147">BackgroundService 基类</span><span class="sxs-lookup"><span data-stu-id="f7273-147">BackgroundService base class</span></span>

<span data-ttu-id="f7273-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> 是用于实现长时间运行的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的基类。</span><span class="sxs-lookup"><span data-stu-id="f7273-148"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="f7273-149">调用 [ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) 来运行后台服务。</span><span class="sxs-lookup"><span data-stu-id="f7273-149">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="f7273-150">实现返回一个 <xref:System.Threading.Tasks.Task>，其表示后台服务的整个生存期。</span><span class="sxs-lookup"><span data-stu-id="f7273-150">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="f7273-151">在 [ExecuteAsync 变为异步](https://github.com/aspnet/Extensions/issues/2149)（例如通过调用 `await`）之前，不会启动任何其他服务。</span><span class="sxs-lookup"><span data-stu-id="f7273-151">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/aspnet/Extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="f7273-152">避免在 `ExecuteAsync` 中执行长时间的阻塞初始化工作。</span><span class="sxs-lookup"><span data-stu-id="f7273-152">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span> <span data-ttu-id="f7273-153">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) 中的主机块等待完成 `ExecuteAsync`。</span><span class="sxs-lookup"><span data-stu-id="f7273-153">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="f7273-154">调用 [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) 时，将触发取消令牌。</span><span class="sxs-lookup"><span data-stu-id="f7273-154">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="f7273-155">当激发取消令牌以便正常关闭服务时，`ExecuteAsync` 的实现应立即完成。</span><span class="sxs-lookup"><span data-stu-id="f7273-155">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="f7273-156">否则，服务将在关闭超时后不正常关闭。</span><span class="sxs-lookup"><span data-stu-id="f7273-156">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span> <span data-ttu-id="f7273-157">有关更多信息，请参阅 [IHostedService interface](#ihostedservice-interface) 部分。</span><span class="sxs-lookup"><span data-stu-id="f7273-157">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="f7273-158">计时的后台任务</span><span class="sxs-lookup"><span data-stu-id="f7273-158">Timed background tasks</span></span>

<span data-ttu-id="f7273-159">定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。</span><span class="sxs-lookup"><span data-stu-id="f7273-159">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="f7273-160">计时器触发任务的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="f7273-160">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="f7273-161">在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：</span><span class="sxs-lookup"><span data-stu-id="f7273-161">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="f7273-162">已使用 `AddHostedService` 扩展方法在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册该服务：</span><span class="sxs-lookup"><span data-stu-id="f7273-162">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="f7273-163">在后台任务中使用有作用域的服务</span><span class="sxs-lookup"><span data-stu-id="f7273-163">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="f7273-164">要在 [BackgroundService](#backgroundservice-base-class) 中使用[有作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)，请创建作用域。</span><span class="sxs-lookup"><span data-stu-id="f7273-164">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="f7273-165">默认情况下，不会为托管服务创建作用域。</span><span class="sxs-lookup"><span data-stu-id="f7273-165">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="f7273-166">作用域后台任务服务包含后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f7273-166">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="f7273-167">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="f7273-167">In the following example:</span></span>

* <span data-ttu-id="f7273-168">服务是异步的。</span><span class="sxs-lookup"><span data-stu-id="f7273-168">The service is asynchronous.</span></span> <span data-ttu-id="f7273-169">`DoWork` 方法返回 `Task`。</span><span class="sxs-lookup"><span data-stu-id="f7273-169">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="f7273-170">出于演示目的，在 `DoWork` 方法中等待 10 秒的延迟。</span><span class="sxs-lookup"><span data-stu-id="f7273-170">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="f7273-171"><xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中。</span><span class="sxs-lookup"><span data-stu-id="f7273-171">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="f7273-172">托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="f7273-172">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="f7273-173">`DoWork` 返回 `ExecuteAsync` 等待的 `Task`：</span><span class="sxs-lookup"><span data-stu-id="f7273-173">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="f7273-174">已在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="f7273-174">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="f7273-175">已使用 `AddHostedService` 扩展方法注册托管服务：</span><span class="sxs-lookup"><span data-stu-id="f7273-175">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="f7273-176">排队的后台任务</span><span class="sxs-lookup"><span data-stu-id="f7273-176">Queued background tasks</span></span>

<span data-ttu-id="f7273-177">后台任务队列基于 .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>（[暂定为 ASP.NET Core 内置版本](https://github.com/aspnet/Hosting/issues/1280)）：</span><span class="sxs-lookup"><span data-stu-id="f7273-177">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="f7273-178">在以下 `QueueHostedService` 示例中：</span><span class="sxs-lookup"><span data-stu-id="f7273-178">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="f7273-179">`BackgroundProcessing` 方法返回 `ExecuteAsync` 中等待的 `Task`。</span><span class="sxs-lookup"><span data-stu-id="f7273-179">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="f7273-180">在 `BackgroundProcessing` 中，取消排队并执行队列中的后台任务。</span><span class="sxs-lookup"><span data-stu-id="f7273-180">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="f7273-181">服务在 `StopAsync` 中停止之前，将等待工作项。</span><span class="sxs-lookup"><span data-stu-id="f7273-181">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="f7273-182">每当在输入设备上选择 `w` 键时，`MonitorLoop` 服务将处理托管服务的排队任务：</span><span class="sxs-lookup"><span data-stu-id="f7273-182">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="f7273-183">`IBackgroundTaskQueue` 注入到 `MonitorLoop` 服务中。</span><span class="sxs-lookup"><span data-stu-id="f7273-183">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="f7273-184">调用 `IBackgroundTaskQueue.QueueBackgroundWorkItem` 来将工作项排入队列。</span><span class="sxs-lookup"><span data-stu-id="f7273-184">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="f7273-185">工作项模拟长时间运行的后台任务：</span><span class="sxs-lookup"><span data-stu-id="f7273-185">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="f7273-186">将执行三次 5 秒的延迟 (`Task.Delay`)。</span><span class="sxs-lookup"><span data-stu-id="f7273-186">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="f7273-187">如果任务已取消，`try-catch` 语句将捕获 <xref:System.OperationCanceledException>。</span><span class="sxs-lookup"><span data-stu-id="f7273-187">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="f7273-188">已在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="f7273-188">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="f7273-189">已使用 `AddHostedService` 扩展方法注册托管服务：</span><span class="sxs-lookup"><span data-stu-id="f7273-189">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f7273-190">在 ASP.NET Core 中，后台任务作为托管服务实现  。</span><span class="sxs-lookup"><span data-stu-id="f7273-190">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="f7273-191">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="f7273-191">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="f7273-192">本主题提供了三个托管服务示例：</span><span class="sxs-lookup"><span data-stu-id="f7273-192">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="f7273-193">在计时器上运行的后台任务。</span><span class="sxs-lookup"><span data-stu-id="f7273-193">Background task that runs on a timer.</span></span>
* <span data-ttu-id="f7273-194">激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。</span><span class="sxs-lookup"><span data-stu-id="f7273-194">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="f7273-195">有作用域的服务可使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="f7273-195">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="f7273-196">按顺序运行的已排队后台任务。</span><span class="sxs-lookup"><span data-stu-id="f7273-196">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="f7273-197">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f7273-197">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="f7273-198">Package</span><span class="sxs-lookup"><span data-stu-id="f7273-198">Package</span></span>

<span data-ttu-id="f7273-199">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包。</span><span class="sxs-lookup"><span data-stu-id="f7273-199">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="f7273-200">IHostedService 接口</span><span class="sxs-lookup"><span data-stu-id="f7273-200">IHostedService interface</span></span>

<span data-ttu-id="f7273-201">托管服务实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口。</span><span class="sxs-lookup"><span data-stu-id="f7273-201">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="f7273-202">该接口为主机托管的对象定义了两种方法：</span><span class="sxs-lookup"><span data-stu-id="f7273-202">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="f7273-203">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` 包含启动后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f7273-203">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="f7273-204">当使用 [Web 主机](xref:fundamentals/host/web-host)时，会在启动服务器并触发 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) 后调用 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="f7273-204">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="f7273-205">当使用[通用主机](xref:fundamentals/host/generic-host)时，会在触发 `ApplicationStarted` 之前调用 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="f7273-205">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="f7273-206">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; 主机正常关闭时触发。</span><span class="sxs-lookup"><span data-stu-id="f7273-206">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="f7273-207">`StopAsync` 包含结束后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f7273-207">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="f7273-208">实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。</span><span class="sxs-lookup"><span data-stu-id="f7273-208">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="f7273-209">默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。</span><span class="sxs-lookup"><span data-stu-id="f7273-209">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="f7273-210">在令牌上请求取消时：</span><span class="sxs-lookup"><span data-stu-id="f7273-210">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="f7273-211">应中止应用正在执行的任何剩余后台操作。</span><span class="sxs-lookup"><span data-stu-id="f7273-211">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="f7273-212">`StopAsync` 中调用的任何方法都应及时返回。</span><span class="sxs-lookup"><span data-stu-id="f7273-212">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="f7273-213">但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。</span><span class="sxs-lookup"><span data-stu-id="f7273-213">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="f7273-214">如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="f7273-214">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="f7273-215">因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。</span><span class="sxs-lookup"><span data-stu-id="f7273-215">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="f7273-216">若要延长默认值为 5 秒的关闭超时值，请设置：</span><span class="sxs-lookup"><span data-stu-id="f7273-216">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="f7273-217"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>（当使用通用主机时）。</span><span class="sxs-lookup"><span data-stu-id="f7273-217"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="f7273-218">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="f7273-218">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="f7273-219">使用 Web 主机时为关闭超时值主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="f7273-219">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="f7273-220">有关详细信息，请参阅 <xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="f7273-220">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="f7273-221">托管服务在应用启动时激活一次，在应用关闭时正常关闭。</span><span class="sxs-lookup"><span data-stu-id="f7273-221">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="f7273-222">如果在执行后台任务期间引发错误，即使未调用 `StopAsync`，也应调用 `Dispose`。</span><span class="sxs-lookup"><span data-stu-id="f7273-222">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="f7273-223">计时的后台任务</span><span class="sxs-lookup"><span data-stu-id="f7273-223">Timed background tasks</span></span>

<span data-ttu-id="f7273-224">定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。</span><span class="sxs-lookup"><span data-stu-id="f7273-224">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="f7273-225">计时器触发任务的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="f7273-225">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="f7273-226">在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：</span><span class="sxs-lookup"><span data-stu-id="f7273-226">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="f7273-227">已使用 `AddHostedService` 扩展方法在 `Startup.ConfigureServices` 中注册该服务：</span><span class="sxs-lookup"><span data-stu-id="f7273-227">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="f7273-228">在后台任务中使用有作用域的服务</span><span class="sxs-lookup"><span data-stu-id="f7273-228">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="f7273-229">要在 `IHostedService` 中使用[有作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)，请创建一个作用域。</span><span class="sxs-lookup"><span data-stu-id="f7273-229">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="f7273-230">默认情况下，不会为托管服务创建作用域。</span><span class="sxs-lookup"><span data-stu-id="f7273-230">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="f7273-231">作用域后台任务服务包含后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f7273-231">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="f7273-232">在以下示例中，将 <xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中：</span><span class="sxs-lookup"><span data-stu-id="f7273-232">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="f7273-233">托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法：</span><span class="sxs-lookup"><span data-stu-id="f7273-233">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="f7273-234">已在 `Startup.ConfigureServices` 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="f7273-234">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f7273-235">已使用 `AddHostedService` 扩展方法注册 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="f7273-235">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="f7273-236">排队的后台任务</span><span class="sxs-lookup"><span data-stu-id="f7273-236">Queued background tasks</span></span>

<span data-ttu-id="f7273-237">后台任务队列基于 .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>（[暂定为 ASP.NET Core 内置版本](https://github.com/aspnet/Hosting/issues/1280)）：</span><span class="sxs-lookup"><span data-stu-id="f7273-237">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="f7273-238">在 `QueueHostedService` 中，队列中的后台任务会取消排队，并作为 [BackgroundService](#backgroundservice-base-class) 执行，此类是用于实现长时间运行 `IHostedService` 的基类：</span><span class="sxs-lookup"><span data-stu-id="f7273-238">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="f7273-239">已在 `Startup.ConfigureServices` 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="f7273-239">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f7273-240">已使用 `AddHostedService` 扩展方法注册 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="f7273-240">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="f7273-241">在索引页模型类中：</span><span class="sxs-lookup"><span data-stu-id="f7273-241">In the Index page model class:</span></span>

* <span data-ttu-id="f7273-242">将 `IBackgroundTaskQueue` 注入构造函数并分配给 `Queue`。</span><span class="sxs-lookup"><span data-stu-id="f7273-242">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="f7273-243">注入 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 并将其分配给 `_serviceScopeFactory`。</span><span class="sxs-lookup"><span data-stu-id="f7273-243">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="f7273-244">工厂用于创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 的实例，用于在范围内创建服务。</span><span class="sxs-lookup"><span data-stu-id="f7273-244">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="f7273-245">创建范围是为了使用应用的`AppDbContext`（[设置了范围的服务](xref:fundamentals/dependency-injection#service-lifetimes)），以在 `IBackgroundTaskQueue`（单一实例服务）中写入数据库记录。</span><span class="sxs-lookup"><span data-stu-id="f7273-245">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="f7273-246">在索引页上选择“添加任务”按钮时，会执行 `OnPostAddTask` 方法  。</span><span class="sxs-lookup"><span data-stu-id="f7273-246">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="f7273-247">调用 `QueueBackgroundWorkItem` 来将工作项排入队列：</span><span class="sxs-lookup"><span data-stu-id="f7273-247">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f7273-248">其他资源</span><span class="sxs-lookup"><span data-stu-id="f7273-248">Additional resources</span></span>

* [<span data-ttu-id="f7273-249">使用 IHostedService 和 BackgroundService 类在微服务中实现后台任务</span><span class="sxs-lookup"><span data-stu-id="f7273-249">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
