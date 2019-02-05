---
title: 在后台服务主机 ASP.NET Core SignalR
author: bradygaster
description: 了解如何从.NET Core BackgroundService 类将消息发送到 SignalR 客户端。
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: b359bd7f6b0667aeb8d9c8f5eb450637b1347b19
ms.sourcegitcommit: e418cb9cddeb3de06fa0cb4fdb5529da03ff6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55739665"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="b17fc-103">在后台服务主机 ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="b17fc-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="b17fc-104">通过[Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="b17fc-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="b17fc-105">本文提供了指南：</span><span class="sxs-lookup"><span data-stu-id="b17fc-105">This article provides guidance for:</span></span>

* <span data-ttu-id="b17fc-106">托管 SignalR 集线器使用宿主使用 ASP.NET Core 的后台工作进程。</span><span class="sxs-lookup"><span data-stu-id="b17fc-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="b17fc-107">将消息发送到连接从.NET Core 中的客户端[BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)。</span><span class="sxs-lookup"><span data-stu-id="b17fc-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="b17fc-108">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/background-service/sample/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b17fc-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-signalr-during-startup"></a><span data-ttu-id="b17fc-109">在启动期间将 SignalR</span><span class="sxs-lookup"><span data-stu-id="b17fc-109">Wire up SignalR during startup</span></span>

<span data-ttu-id="b17fc-110">在后台工作进程的上下文中承载 ASP.NET Core SignalR 集线器等同于承载的 ASP.NET Core web 应用中的中心。</span><span class="sxs-lookup"><span data-stu-id="b17fc-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="b17fc-111">在中`Startup.ConfigureServices`方法中，调用`services.AddSignalR`将所需的服务添加到 ASP.NET Core 依赖关系注入 (DI) 层以支持 SignalR。</span><span class="sxs-lookup"><span data-stu-id="b17fc-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="b17fc-112">在中`Startup.Configure`，则`UseSignalR`调用方法到 ASP.NET Core 请求管道中的中心终结点关联。</span><span class="sxs-lookup"><span data-stu-id="b17fc-112">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

<span data-ttu-id="b17fc-113">在前面的示例中，`ClockHub`类实现`Hub<T>`类，以创建强类型化的中心。</span><span class="sxs-lookup"><span data-stu-id="b17fc-113">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="b17fc-114">`ClockHub`已在中配置`Startup`类，以响应在终结点请求`/hubs/clock`。</span><span class="sxs-lookup"><span data-stu-id="b17fc-114">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="b17fc-115">强类型化的中心的详细信息，请参阅[适用于 ASP.NET Core SignalR 中使用中心](xref:signalr/hubs#strongly-typed-hubs)。</span><span class="sxs-lookup"><span data-stu-id="b17fc-115">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="b17fc-116">此功能并不局限于[集线器\<T >](xref:Microsoft.AspNetCore.SignalR.Hub`1)类。</span><span class="sxs-lookup"><span data-stu-id="b17fc-116">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="b17fc-117">任何类都继承自[集线器](xref:Microsoft.AspNetCore.SignalR.Hub)，如[DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)，也将起作用。</span><span class="sxs-lookup"><span data-stu-id="b17fc-117">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="b17fc-118">使用强类型化的接口`ClockHub`是`IClock`接口。</span><span class="sxs-lookup"><span data-stu-id="b17fc-118">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="b17fc-119">从后台服务调用 SignalR Hub</span><span class="sxs-lookup"><span data-stu-id="b17fc-119">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="b17fc-120">启动过程中，`Worker`类， `BackgroundService`，使用有线`AddHostedService`。</span><span class="sxs-lookup"><span data-stu-id="b17fc-120">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="b17fc-121">由于 SignalR 还有线期间`Startup`阶段时，在其中每个中心已附加到 ASP.NET Core 的 HTTP 请求管道中的单个终结点，由表示每个中心`IHubContext<T>`在服务器上。</span><span class="sxs-lookup"><span data-stu-id="b17fc-121">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="b17fc-122">使用 ASP.NET Core DI 功能，如由托管层实例化其他类`BackgroundService`类、 MVC 控制器类或 Razor 页面模型，可以通过接受的实例来获取对服务器端集线器的引用`IHubContext<ClockHub, IClock>`在构造期间。</span><span class="sxs-lookup"><span data-stu-id="b17fc-122">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="b17fc-123">作为`ExecuteAsync`以迭代方式调用方法时在后台服务，服务器的当前日期和时间发送到连接的客户端使用`ClockHub`。</span><span class="sxs-lookup"><span data-stu-id="b17fc-123">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="b17fc-124">对使用后台服务 SignalR 事件做出响应</span><span class="sxs-lookup"><span data-stu-id="b17fc-124">React to SignalR events with background services</span></span>

<span data-ttu-id="b17fc-125">像使用 JavaScript 客户端 SignalR 或.NET 桌面应用程序可以执行的单页面应用程序使用的 using <xref:signalr/dotnet-client>、 一个`BackgroundService`或`IHostedService`实现还可用来连接到 SignalR 集线器和响应事件。</span><span class="sxs-lookup"><span data-stu-id="b17fc-125">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="b17fc-126">`ClockHubClient`类可实现`IClock`界面和`IHostedService`接口。</span><span class="sxs-lookup"><span data-stu-id="b17fc-126">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="b17fc-127">它可以建立连接，在这种方式`Startup`连续运行并响应来自服务器的中心事件。</span><span class="sxs-lookup"><span data-stu-id="b17fc-127">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span> 

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="b17fc-128">在初始化期间`ClockHubClient`创建的实例`HubConnection`和绑定`IClock.ShowTime`方法的处理程序为中心的`ShowTime`事件。</span><span class="sxs-lookup"><span data-stu-id="b17fc-128">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="b17fc-129">在中`IHostedService.StartAsync`实现中，`HubConnection`以异步方式启动。</span><span class="sxs-lookup"><span data-stu-id="b17fc-129">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="b17fc-130">期间`IHostedService.StopAsync`方法，`HubConnection`以异步方式释放。</span><span class="sxs-lookup"><span data-stu-id="b17fc-130">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="b17fc-131">其他资源</span><span class="sxs-lookup"><span data-stu-id="b17fc-131">Additional resources</span></span>

* [<span data-ttu-id="b17fc-132">入门</span><span class="sxs-lookup"><span data-stu-id="b17fc-132">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b17fc-133">中心</span><span class="sxs-lookup"><span data-stu-id="b17fc-133">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b17fc-134">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="b17fc-134">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="b17fc-135">强类型化的中心</span><span class="sxs-lookup"><span data-stu-id="b17fc-135">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
