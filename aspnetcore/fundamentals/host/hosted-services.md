---
title: 在 ASP.NET Core 中使用托管服务实现后台任务
author: guardrex
description: 了解如何在 ASP.NET Core 中使用托管服务实现后台任务。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/03/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 3d4279a291182da60c0cb2fbb93a3922ed673cde
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914021"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="1960d-103">在 ASP.NET Core 中使用托管服务实现后台任务</span><span class="sxs-lookup"><span data-stu-id="1960d-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="1960d-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1960d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1960d-105">在 ASP.NET Core 中，后台任务作为托管服务实现  。</span><span class="sxs-lookup"><span data-stu-id="1960d-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="1960d-106">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="1960d-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="1960d-107">本主题提供了三个托管服务示例：</span><span class="sxs-lookup"><span data-stu-id="1960d-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="1960d-108">在计时器上运行的后台任务。</span><span class="sxs-lookup"><span data-stu-id="1960d-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="1960d-109">激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。</span><span class="sxs-lookup"><span data-stu-id="1960d-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="1960d-110">有作用域的服务可使用依赖项注入。</span><span class="sxs-lookup"><span data-stu-id="1960d-110">The scoped service can use dependency injection.</span></span>
* <span data-ttu-id="1960d-111">按顺序运行的已排队后台任务。</span><span class="sxs-lookup"><span data-stu-id="1960d-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="1960d-112">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1960d-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1960d-113">此示例应用分为两个版本：</span><span class="sxs-lookup"><span data-stu-id="1960d-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="1960d-114">Web 主机 &ndash; Web 主机可用于托管 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="1960d-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="1960d-115">本主题中所示的示例代码来自示例的 Web 主机版本。</span><span class="sxs-lookup"><span data-stu-id="1960d-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="1960d-116">有关详细信息，请参阅 [Web 主机](xref:fundamentals/host/web-host)主题。</span><span class="sxs-lookup"><span data-stu-id="1960d-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="1960d-117">通用主机 &ndash; 通用主机是 ASP.NET Core 2.1 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="1960d-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="1960d-118">有关详细信息，请参阅[通用主机](xref:fundamentals/host/generic-host)主题。</span><span class="sxs-lookup"><span data-stu-id="1960d-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="1960d-119">辅助角色服务模板</span><span class="sxs-lookup"><span data-stu-id="1960d-119">Worker Service template</span></span>

<span data-ttu-id="1960d-120">ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。</span><span class="sxs-lookup"><span data-stu-id="1960d-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="1960d-121">要使用该模板作为编写托管服务应用的基础：</span><span class="sxs-lookup"><span data-stu-id="1960d-121">To use the template as a basis for a hosted services app:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1960d-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1960d-122">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1960d-123">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="1960d-123">Create a new project.</span></span>
1. <span data-ttu-id="1960d-124">选择“ASP.NET Core Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="1960d-124">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="1960d-125">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="1960d-125">Select **Next**.</span></span>
1. <span data-ttu-id="1960d-126">在“项目名称”字段提供项目名称，或接受默认项目名称  。</span><span class="sxs-lookup"><span data-stu-id="1960d-126">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="1960d-127">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="1960d-127">Select **Create**.</span></span>
1. <span data-ttu-id="1960d-128">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="1960d-128">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="1960d-129">选择“辅助角色服务”模板  。</span><span class="sxs-lookup"><span data-stu-id="1960d-129">Select the **Worker Service** template.</span></span> <span data-ttu-id="1960d-130">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="1960d-130">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1960d-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1960d-131">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1960d-132">将辅助角色服务 (`worker`) 模板用于命令行界面中的 [dotnet new](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="1960d-132">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="1960d-133">下面的示例中创建了名为 `ContosoWorkerService` 的辅助角色服务应用。</span><span class="sxs-lookup"><span data-stu-id="1960d-133">In the following example, a Worker Service app is created named `ContosoWorkerService`.</span></span> <span data-ttu-id="1960d-134">执行命令时会自动为 `ContosoWorkerService` 应用创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="1960d-134">A folder for the `ContosoWorkerService` app is created automatically when the command is executed.</span></span>

```console
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="package"></a><span data-ttu-id="1960d-135">Package</span><span class="sxs-lookup"><span data-stu-id="1960d-135">Package</span></span>

<span data-ttu-id="1960d-136">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包。</span><span class="sxs-lookup"><span data-stu-id="1960d-136">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="1960d-137">IHostedService 接口</span><span class="sxs-lookup"><span data-stu-id="1960d-137">IHostedService interface</span></span>

<span data-ttu-id="1960d-138">托管服务实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口。</span><span class="sxs-lookup"><span data-stu-id="1960d-138">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="1960d-139">该接口为主机托管的对象定义了两种方法：</span><span class="sxs-lookup"><span data-stu-id="1960d-139">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="1960d-140">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` 包含启动后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="1960d-140">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="1960d-141">当使用 [Web 主机](xref:fundamentals/host/web-host)时，会在启动服务器并触发 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) 后调用 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="1960d-141">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="1960d-142">当使用[通用主机](xref:fundamentals/host/generic-host)时，会在触发 `ApplicationStarted` 之前调用 `StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="1960d-142">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="1960d-143">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; 主机正常关闭时触发。</span><span class="sxs-lookup"><span data-stu-id="1960d-143">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="1960d-144">`StopAsync` 包含结束后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="1960d-144">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="1960d-145">实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。</span><span class="sxs-lookup"><span data-stu-id="1960d-145">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="1960d-146">默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。</span><span class="sxs-lookup"><span data-stu-id="1960d-146">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="1960d-147">在令牌上请求取消时：</span><span class="sxs-lookup"><span data-stu-id="1960d-147">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="1960d-148">应中止应用正在执行的任何剩余后台操作。</span><span class="sxs-lookup"><span data-stu-id="1960d-148">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="1960d-149">`StopAsync` 中调用的任何方法都应及时返回。</span><span class="sxs-lookup"><span data-stu-id="1960d-149">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="1960d-150">但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。</span><span class="sxs-lookup"><span data-stu-id="1960d-150">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="1960d-151">如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。</span><span class="sxs-lookup"><span data-stu-id="1960d-151">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="1960d-152">因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。</span><span class="sxs-lookup"><span data-stu-id="1960d-152">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="1960d-153">若要延长默认值为 5 秒的关闭超时值，请设置：</span><span class="sxs-lookup"><span data-stu-id="1960d-153">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="1960d-154"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 何时使用泛型主机。</span><span class="sxs-lookup"><span data-stu-id="1960d-154"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="1960d-155">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="1960d-155">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="1960d-156">使用 Web 主机时为关闭超时值主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="1960d-156">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="1960d-157">有关详细信息，请参阅 <xref:fundamentals/host/web-host#shutdown-timeout>。</span><span class="sxs-lookup"><span data-stu-id="1960d-157">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="1960d-158">托管服务在应用启动时激活一次，在应用关闭时正常关闭。</span><span class="sxs-lookup"><span data-stu-id="1960d-158">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="1960d-159">如果在执行后台任务期间引发错误，即使未调用 `StopAsync`，也应调用 `Dispose`。</span><span class="sxs-lookup"><span data-stu-id="1960d-159">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="1960d-160">计时的后台任务</span><span class="sxs-lookup"><span data-stu-id="1960d-160">Timed background tasks</span></span>

<span data-ttu-id="1960d-161">定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。</span><span class="sxs-lookup"><span data-stu-id="1960d-161">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="1960d-162">计时器触发任务的 `DoWork` 方法。</span><span class="sxs-lookup"><span data-stu-id="1960d-162">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="1960d-163">在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：</span><span class="sxs-lookup"><span data-stu-id="1960d-163">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="1960d-164">已使用 `AddHostedService` 扩展方法在 `Startup.ConfigureServices` 中注册该服务：</span><span class="sxs-lookup"><span data-stu-id="1960d-164">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="1960d-165">在后台任务中使用有作用域的服务</span><span class="sxs-lookup"><span data-stu-id="1960d-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="1960d-166">要在 `IHostedService` 中使用[有作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)，请创建一个作用域。</span><span class="sxs-lookup"><span data-stu-id="1960d-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="1960d-167">默认情况下，不会为托管服务创建作用域。</span><span class="sxs-lookup"><span data-stu-id="1960d-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="1960d-168">作用域后台任务服务包含后台任务的逻辑。</span><span class="sxs-lookup"><span data-stu-id="1960d-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="1960d-169">在以下示例中，将 <xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中：</span><span class="sxs-lookup"><span data-stu-id="1960d-169">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="1960d-170">托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法：</span><span class="sxs-lookup"><span data-stu-id="1960d-170">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="1960d-171">已在 `Startup.ConfigureServices` 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="1960d-171">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1960d-172">已使用 `AddHostedService` 扩展方法注册 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="1960d-172">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="1960d-173">排队的后台任务</span><span class="sxs-lookup"><span data-stu-id="1960d-173">Queued background tasks</span></span>

<span data-ttu-id="1960d-174">后台任务队列基于 .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>（[暂定为 ASP.NET Core 3.0 内置版本](https://github.com/aspnet/Hosting/issues/1280)）：</span><span class="sxs-lookup"><span data-stu-id="1960d-174">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core 3.0](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="1960d-175">在 `QueueHostedService` 中，队列中的后台任务会取消排队，并作为 <xref:Microsoft.Extensions.Hosting.BackgroundService> 执行，此类是用于实现长时间运行 `IHostedService` 的基类：</span><span class="sxs-lookup"><span data-stu-id="1960d-175">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="1960d-176">已在 `Startup.ConfigureServices` 中注册这些服务。</span><span class="sxs-lookup"><span data-stu-id="1960d-176">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1960d-177">已使用 `AddHostedService` 扩展方法注册 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="1960d-177">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet3)]

<span data-ttu-id="1960d-178">在索引页模型类中：</span><span class="sxs-lookup"><span data-stu-id="1960d-178">In the Index page model class:</span></span>

* <span data-ttu-id="1960d-179">将 `IBackgroundTaskQueue` 注入构造函数并分配给 `Queue`。</span><span class="sxs-lookup"><span data-stu-id="1960d-179">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="1960d-180">注入 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 并将其分配给 `_serviceScopeFactory`。</span><span class="sxs-lookup"><span data-stu-id="1960d-180">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="1960d-181">工厂用于创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 的实例，用于在范围内创建服务。</span><span class="sxs-lookup"><span data-stu-id="1960d-181">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="1960d-182">创建范围是为了使用应用的`AppDbContext`（[设置了范围的服务](xref:fundamentals/dependency-injection#service-lifetimes)），以在 `IBackgroundTaskQueue`（单一实例服务）中写入数据库记录。</span><span class="sxs-lookup"><span data-stu-id="1960d-182">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="1960d-183">在索引页上选择“添加任务”按钮时，会执行 `OnPostAddTask` 方法  。</span><span class="sxs-lookup"><span data-stu-id="1960d-183">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="1960d-184">调用 `QueueBackgroundWorkItem` 来将工作项排入队列：</span><span class="sxs-lookup"><span data-stu-id="1960d-184">`QueueBackgroundWorkItem` is called to enqueue the work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="additional-resources"></a><span data-ttu-id="1960d-185">其他资源</span><span class="sxs-lookup"><span data-stu-id="1960d-185">Additional resources</span></span>

* [<span data-ttu-id="1960d-186">使用 IHostedService 和 BackgroundService 类在微服务中实现后台任务</span><span class="sxs-lookup"><span data-stu-id="1960d-186">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
