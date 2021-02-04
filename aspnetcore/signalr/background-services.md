---
title: SignalR在后台服务中托管 ASP.NET Core
author: bradygaster
description: 了解如何 SignalR 通过 .Net Core BackgroundService 类将消息发送到客户端。
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 4b877c64a881fec15ac9e9bd74ffdde0b5fa60f9
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530172"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="f9b05-103">SignalR在后台服务中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9b05-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="f9b05-104">作者： [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="f9b05-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="f9b05-105">本文提供以下内容的指导：</span><span class="sxs-lookup"><span data-stu-id="f9b05-105">This article provides guidance for:</span></span>

* <span data-ttu-id="f9b05-106">SignalR使用宿主 ASP.NET Core 的后台工作进程托管中心。</span><span class="sxs-lookup"><span data-stu-id="f9b05-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="f9b05-107">从 .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)中将消息发送到已连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="f9b05-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9b05-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x)[（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f9b05-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f9b05-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2)[（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f9b05-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="f9b05-110">SignalR在启动时启用</span><span class="sxs-lookup"><span data-stu-id="f9b05-110">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9b05-111">SignalR在后台工作进程的上下文中托管 ASP.NET Core 中心与在 ASP.NET Core 的 web 应用中托管集线器完全相同。</span><span class="sxs-lookup"><span data-stu-id="f9b05-111">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="f9b05-112">在 `Startup.ConfigureServices` 方法中，调用会 `services.AddSignalR` 将所需的服务添加到 ASP.NET Core 依赖关系注入 (DI) 层以支持 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f9b05-112">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="f9b05-113">在中 `Startup.Configure` ， `MapHub` 调用方法 `UseEndpoints` 以连接 ASP.NET Core 请求管道中的中心终结点。</span><span class="sxs-lookup"><span data-stu-id="f9b05-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f9b05-114">SignalR在后台工作进程的上下文中托管 ASP.NET Core 中心与在 ASP.NET Core 的 web 应用中托管集线器完全相同。</span><span class="sxs-lookup"><span data-stu-id="f9b05-114">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="f9b05-115">在 `Startup.ConfigureServices` 方法中，调用会 `services.AddSignalR` 将所需的服务添加到 ASP.NET Core 依赖关系注入 (DI) 层以支持 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f9b05-115">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="f9b05-116">在中 `Startup.Configure` ， `UseSignalR` 调用方法以将中心终结点连接到 ASP.NET Core 请求管道中的)  (。</span><span class="sxs-lookup"><span data-stu-id="f9b05-116">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="f9b05-117">在前面的示例中， `ClockHub` 类实现 `Hub<T>` 类以创建强类型中心。</span><span class="sxs-lookup"><span data-stu-id="f9b05-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="f9b05-118">已 `ClockHub` 在类中配置， `Startup` 以响应终结点上的请求 `/hubs/clock` 。</span><span class="sxs-lookup"><span data-stu-id="f9b05-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="f9b05-119">有关强类型化集线器的详细信息，请参阅 [使用中 SignalR 的中心进行 ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs)。</span><span class="sxs-lookup"><span data-stu-id="f9b05-119">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="f9b05-120">此功能并不限于[集线器 \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1)类。</span><span class="sxs-lookup"><span data-stu-id="f9b05-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="f9b05-121">从 [中心](xref:Microsoft.AspNetCore.SignalR.Hub)继承的任何类（如 [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)）都适用。</span><span class="sxs-lookup"><span data-stu-id="f9b05-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="f9b05-122">强类型化所使用的接口 `ClockHub` 是 `IClock` 接口。</span><span class="sxs-lookup"><span data-stu-id="f9b05-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="f9b05-123">SignalR从后台服务调用中心</span><span class="sxs-lookup"><span data-stu-id="f9b05-123">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="f9b05-124">在启动过程中， `Worker` `BackgroundService` 使用启用类 `AddHostedService` 。</span><span class="sxs-lookup"><span data-stu-id="f9b05-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="f9b05-125">由于 SignalR 还会在 `Startup` 阶段启用，其中每个中心均附加到 ASP.NET CORE 的 HTTP 请求管道中的单个终结点，因此，每个中心都由 `IHubContext<T>` 服务器上的表示。</span><span class="sxs-lookup"><span data-stu-id="f9b05-125">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="f9b05-126">使用 ASP.NET Core 的 DI 功能，由宿主层实例化的其他类（如 `BackgroundService` 类、MVC 控制器类或 Razor 页模型）可以 `IHubContext<ClockHub, IClock>` 在构造过程中接受实例，从而获取对服务器端集线器的引用。</span><span class="sxs-lookup"><span data-stu-id="f9b05-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="f9b05-127">由于 `ExecuteAsync` 在后台服务中以迭代方式调用方法，服务器的当前日期和时间将使用发送到已连接的客户端 `ClockHub` 。</span><span class="sxs-lookup"><span data-stu-id="f9b05-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="f9b05-128">SignalR通过后台服务对事件做出响应</span><span class="sxs-lookup"><span data-stu-id="f9b05-128">React to SignalR events with background services</span></span>

<span data-ttu-id="f9b05-129">与使用适用于的 JavaScript 客户端的单页面应用 SignalR 或使用的 .net 桌面应用一样 <xref:signalr/dotnet-client> ， `BackgroundService` 或 `IHostedService` 实现也可用于连接到 SignalR 集线器和响应事件。</span><span class="sxs-lookup"><span data-stu-id="f9b05-129">Like a Single Page App using the JavaScript client for SignalR, or a .NET desktop app using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="f9b05-130">`ClockHubClient`类实现 `IClock` 接口和 `IHostedService` 接口。</span><span class="sxs-lookup"><span data-stu-id="f9b05-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="f9b05-131">这样一来，就可以在过程中将其启用 `Startup` ，并对来自服务器的中心事件做出响应。</span><span class="sxs-lookup"><span data-stu-id="f9b05-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="f9b05-132">在初始化期间，会 `ClockHubClient` 创建一个实例 `HubConnection` ，并将该 `IClock.ShowTime` 方法作为中心事件的处理程序启用 `ShowTime` 。</span><span class="sxs-lookup"><span data-stu-id="f9b05-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="f9b05-133">在 `IHostedService.StartAsync` 实现中，以 `HubConnection` 异步方式启动。</span><span class="sxs-lookup"><span data-stu-id="f9b05-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="f9b05-134">在 `IHostedService.StopAsync` 方法中，将 `HubConnection` 异步释放。</span><span class="sxs-lookup"><span data-stu-id="f9b05-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="f9b05-135">在 `IHostedService.StartAsync` 实现中，以 `HubConnection` 异步方式启动。</span><span class="sxs-lookup"><span data-stu-id="f9b05-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="f9b05-136">在 `IHostedService.StopAsync` 方法中，将 `HubConnection` 异步释放。</span><span class="sxs-lookup"><span data-stu-id="f9b05-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f9b05-137">其他资源</span><span class="sxs-lookup"><span data-stu-id="f9b05-137">Additional resources</span></span>

* [<span data-ttu-id="f9b05-138">入门</span><span class="sxs-lookup"><span data-stu-id="f9b05-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="f9b05-139">集线器</span><span class="sxs-lookup"><span data-stu-id="f9b05-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f9b05-140">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="f9b05-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="f9b05-141">强类型中心</span><span class="sxs-lookup"><span data-stu-id="f9b05-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
