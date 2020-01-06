---
title: 在后台服务中宿主 ASP.NET Core SignalR
author: bradygaster
description: 了解如何将消息发送到来自 .NET Core BackgroundService 类的 SignalR 客户端。
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 324592759af79d1229eb147fb4551e97c678ef64
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358673"
---
# <a name="host-aspnet-core-opno-locsignalr-in-background-services"></a><span data-ttu-id="e9b13-103">在后台服务中宿主 ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="e9b13-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="e9b13-104">作者： [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="e9b13-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="e9b13-105">本文提供以下内容的指导：</span><span class="sxs-lookup"><span data-stu-id="e9b13-105">This article provides guidance for:</span></span>

* <span data-ttu-id="e9b13-106">使用托管 ASP.NET Core 的后台工作进程承载 SignalR 中心。</span><span class="sxs-lookup"><span data-stu-id="e9b13-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="e9b13-107">从 .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)中将消息发送到已连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="e9b13-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="e9b13-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e9b13-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="enable-opno-locsignalr-in-startup"></a><span data-ttu-id="e9b13-109">启用启动中的 SignalR</span><span class="sxs-lookup"><span data-stu-id="e9b13-109">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e9b13-110">在后台工作进程的上下文中托管 ASP.NET Core SignalR 中心与在 ASP.NET Core web 应用中托管集线器完全相同。</span><span class="sxs-lookup"><span data-stu-id="e9b13-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="e9b13-111">在 `Startup.ConfigureServices` 方法中，调用 `services.AddSignalR` 会将所需的服务添加到 ASP.NET Core 依赖关系注入（DI）层以支持 SignalR。</span><span class="sxs-lookup"><span data-stu-id="e9b13-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="e9b13-112">在 `Startup.Configure`中，`MapHub` 方法在 `UseEndpoints` 回调中调用，以连接 ASP.NET Core 请求管道中的中心终结点。</span><span class="sxs-lookup"><span data-stu-id="e9b13-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e9b13-113">在后台工作进程的上下文中托管 ASP.NET Core SignalR 中心与在 ASP.NET Core web 应用中托管集线器完全相同。</span><span class="sxs-lookup"><span data-stu-id="e9b13-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="e9b13-114">在 `Startup.ConfigureServices` 方法中，调用 `services.AddSignalR` 会将所需的服务添加到 ASP.NET Core 依赖关系注入（DI）层以支持 SignalR。</span><span class="sxs-lookup"><span data-stu-id="e9b13-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="e9b13-115">在 `Startup.Configure`中，调用 `UseSignalR` 方法连接 ASP.NET Core 请求管道中的中心终结点。</span><span class="sxs-lookup"><span data-stu-id="e9b13-115">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="e9b13-116">在前面的示例中，`ClockHub` 类实现了 `Hub<T>` 类，以创建强类型化的中心。</span><span class="sxs-lookup"><span data-stu-id="e9b13-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="e9b13-117">已在 `Startup` 类中配置 `ClockHub`，以响应终结点 `/hubs/clock`的请求。</span><span class="sxs-lookup"><span data-stu-id="e9b13-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="e9b13-118">有关强类型化集线器的详细信息，请参阅[在 SignalR 中使用中心 ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs)。</span><span class="sxs-lookup"><span data-stu-id="e9b13-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="e9b13-119">此功能并不限于[集线器\<t >](xref:Microsoft.AspNetCore.SignalR.Hub`1)类。</span><span class="sxs-lookup"><span data-stu-id="e9b13-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="e9b13-120">从[中心](xref:Microsoft.AspNetCore.SignalR.Hub)继承的任何类（如[DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)）也将起作用。</span><span class="sxs-lookup"><span data-stu-id="e9b13-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="e9b13-121">强类型 `ClockHub` 所使用的接口是 `IClock` 接口。</span><span class="sxs-lookup"><span data-stu-id="e9b13-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-opno-locsignalr-hub-from-a-background-service"></a><span data-ttu-id="e9b13-122">从后台服务调用 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="e9b13-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="e9b13-123">在启动过程中，将使用 `AddHostedService`启用 `Worker` 类 `BackgroundService`。</span><span class="sxs-lookup"><span data-stu-id="e9b13-123">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="e9b13-124">由于 SignalR 也是在 `Startup` 阶段启用的，在这种情况下，每个中心均附加到 ASP.NET Core 的 HTTP 请求管道中的单个终结点，每个中心由服务器上的 `IHubContext<T>` 表示。</span><span class="sxs-lookup"><span data-stu-id="e9b13-124">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="e9b13-125">使用 ASP.NET Core 的 DI 功能，由宿主层实例化的其他类（如 `BackgroundService` 类、MVC 控制器类或 Razor 页面模型）可通过在构造期间接受 `IHubContext<ClockHub, IClock>` 的实例来获取对服务器端集线器的引用。</span><span class="sxs-lookup"><span data-stu-id="e9b13-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="e9b13-126">由于在后台服务中以迭代方式调用 `ExecuteAsync` 方法，因此使用 `ClockHub`将服务器的当前日期和时间发送到已连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="e9b13-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-opno-locsignalr-events-with-background-services"></a><span data-ttu-id="e9b13-127">通过后台服务对 SignalR 事件做出反应</span><span class="sxs-lookup"><span data-stu-id="e9b13-127">React to SignalR events with background services</span></span>

<span data-ttu-id="e9b13-128">与使用 SignalR 的 JavaScript 客户端或 .NET 桌面应用程序一样，使用 JavaScript 客户端的单页面应用程序也可以使用 <xref:signalr/dotnet-client>，`BackgroundService` 或 `IHostedService` 实现也可用于连接到 SignalR 中心和响应事件。</span><span class="sxs-lookup"><span data-stu-id="e9b13-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="e9b13-129">`ClockHubClient` 类既实现了 `IClock` 接口，又实现了 `IHostedService` 接口。</span><span class="sxs-lookup"><span data-stu-id="e9b13-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="e9b13-130">这样，便可以在 `Startup` 期间启用此功能，以便连续运行和响应来自服务器的集线器事件。</span><span class="sxs-lookup"><span data-stu-id="e9b13-130">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="e9b13-131">在初始化期间，`ClockHubClient` 将创建一个 `HubConnection` 的实例，并将 `IClock.ShowTime` 方法启用为中心的 `ShowTime` 事件的处理程序。</span><span class="sxs-lookup"><span data-stu-id="e9b13-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="e9b13-132">在 `IHostedService.StartAsync` 实现中，`HubConnection` 以异步方式启动。</span><span class="sxs-lookup"><span data-stu-id="e9b13-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="e9b13-133">在 `IHostedService.StopAsync` 方法期间，`HubConnection` 将异步释放。</span><span class="sxs-lookup"><span data-stu-id="e9b13-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="e9b13-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="e9b13-134">Additional resources</span></span>

* [<span data-ttu-id="e9b13-135">入门</span><span class="sxs-lookup"><span data-stu-id="e9b13-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="e9b13-136">中心</span><span class="sxs-lookup"><span data-stu-id="e9b13-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="e9b13-137">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="e9b13-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="e9b13-138">强类型中心</span><span class="sxs-lookup"><span data-stu-id="e9b13-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
