---
title: 在后台服务中宿主 ASP.NET Core SignalR
author: bradygaster
description: 了解如何通过 .NET Core BackgroundService 类将消息发送到 SignalR 客户端。
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: 23a53f33a03ce3b76cf6846c3f214a6cad055209
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773941"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="23503-103">在后台服务中宿主 ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="23503-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="23503-104">作者： [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="23503-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="23503-105">本文提供以下内容的指导：</span><span class="sxs-lookup"><span data-stu-id="23503-105">This article provides guidance for:</span></span>

* <span data-ttu-id="23503-106">使用 ASP.NET Core 承载的后台工作进程承载 SignalR 中心。</span><span class="sxs-lookup"><span data-stu-id="23503-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="23503-107">从 .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)中将消息发送到已连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="23503-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="23503-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="23503-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-signalr-during-startup"></a><span data-ttu-id="23503-109">启动时连接 SignalR</span><span class="sxs-lookup"><span data-stu-id="23503-109">Wire up SignalR during startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="23503-110">在后台工作进程的上下文中托管 ASP.NET Core SignalR 中心与在 ASP.NET Core 的 web 应用中托管集线器完全相同。</span><span class="sxs-lookup"><span data-stu-id="23503-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="23503-111">在方法中，调用`services.AddSignalR`会将所需服务添加到 ASP.NET Core 依赖关系注入（DI）层，以支持 SignalR。 `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="23503-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="23503-112">在`Startup.Configure`中`MapHub` ，调用`UseEndpoints`方法以在 ASP.NET Core 请求管道中连接中心终结点。</span><span class="sxs-lookup"><span data-stu-id="23503-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

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

<span data-ttu-id="23503-113">在后台工作进程的上下文中托管 ASP.NET Core SignalR 中心与在 ASP.NET Core 的 web 应用中托管集线器完全相同。</span><span class="sxs-lookup"><span data-stu-id="23503-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="23503-114">在方法中，调用`services.AddSignalR`会将所需服务添加到 ASP.NET Core 依赖关系注入（DI）层，以支持 SignalR。 `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="23503-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="23503-115">在`Startup.Configure`中`UseSignalR` ，调用方法以连接 ASP.NET Core 请求管道中的中心终结点。</span><span class="sxs-lookup"><span data-stu-id="23503-115">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="23503-116">在前面的示例中， `ClockHub`类`Hub<T>`实现类以创建强类型中心。</span><span class="sxs-lookup"><span data-stu-id="23503-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="23503-117">已在类中配置，以响应终结点`/hubs/clock`上的请求。 `Startup` `ClockHub`</span><span class="sxs-lookup"><span data-stu-id="23503-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="23503-118">有关强类型化集线器的详细信息，请参阅[在 SignalR 中使用集线器进行 ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs)。</span><span class="sxs-lookup"><span data-stu-id="23503-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="23503-119">此功能并不限于[\<中心 t >](xref:Microsoft.AspNetCore.SignalR.Hub`1)类。</span><span class="sxs-lookup"><span data-stu-id="23503-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="23503-120">从[中心](xref:Microsoft.AspNetCore.SignalR.Hub)继承的任何类（如[DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)）也将起作用。</span><span class="sxs-lookup"><span data-stu-id="23503-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="23503-121">强类型化`ClockHub`所使用的接口`IClock`是接口。</span><span class="sxs-lookup"><span data-stu-id="23503-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="23503-122">从后台服务调用 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="23503-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="23503-123">在启动过程中`Worker` ，类 a `BackgroundService`使用`AddHostedService`连接。</span><span class="sxs-lookup"><span data-stu-id="23503-123">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="23503-124">由于 SignalR 还`Startup`会在阶段中连接，其中每个集线器都附加到 ASP.NET Core 的 HTTP 请求管道中的单个终结点，因此，每个中心都`IHubContext<T>`由服务器上的表示。</span><span class="sxs-lookup"><span data-stu-id="23503-124">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="23503-125">使用 ASP.NET Core 的 DI 功能，由宿主层实例化的其他类（ `BackgroundService`如类、MVC 控制器类或 Razor 页面模型）可以通过`IHubContext<ClockHub, IClock>`在构造过程中接受实例来获取对服务器端集线器的引用。</span><span class="sxs-lookup"><span data-stu-id="23503-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="23503-126">由于在后台服务中以迭代`ClockHub`方式调用方法，服务器的当前日期和时间将使用发送到已连接的客户端。`ExecuteAsync`</span><span class="sxs-lookup"><span data-stu-id="23503-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="23503-127">通过后台服务响应 SignalR 事件</span><span class="sxs-lookup"><span data-stu-id="23503-127">React to SignalR events with background services</span></span>

<span data-ttu-id="23503-128">与使用适用于 SignalR 的 JavaScript 客户端的单页面应用程序或 .net 桌面应用程序<xref:signalr/dotnet-client>一样`BackgroundService` ，也可以使用、 `IHostedService`或实现来连接到 SignalR 集线器并对事件做出响应。</span><span class="sxs-lookup"><span data-stu-id="23503-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="23503-129">类实现`IClock`接口和`IHostedService`接口。 `ClockHubClient`</span><span class="sxs-lookup"><span data-stu-id="23503-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="23503-130">这样一来，就可以在运行`Startup`时将其连接起来，并响应服务器的集线器事件。</span><span class="sxs-lookup"><span data-stu-id="23503-130">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="23503-131">在初始化期间， `ClockHubClient`会创建一个`HubConnection`实例，并将该`IClock.ShowTime`方法与中心的`ShowTime`事件的处理程序进行连线。</span><span class="sxs-lookup"><span data-stu-id="23503-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="23503-132">在实现中`HubConnection` ，以异步方式启动。 `IHostedService.StartAsync`</span><span class="sxs-lookup"><span data-stu-id="23503-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="23503-133">在方法中`HubConnection` ，将异步释放。 `IHostedService.StopAsync`</span><span class="sxs-lookup"><span data-stu-id="23503-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="23503-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="23503-134">Additional resources</span></span>

* [<span data-ttu-id="23503-135">入门</span><span class="sxs-lookup"><span data-stu-id="23503-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="23503-136">中心</span><span class="sxs-lookup"><span data-stu-id="23503-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="23503-137">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="23503-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="23503-138">强类型中心</span><span class="sxs-lookup"><span data-stu-id="23503-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
