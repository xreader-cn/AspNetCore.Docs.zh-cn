---
title: 在后台服务主机 ASP.NET Core SignalR
author: bradygaster
description: 了解如何从.NET Core BackgroundService 类将消息发送到 SignalR 客户端。
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: dcd62f0c7056a3f987291b6c8bb8b87f94160865
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087758"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>在后台服务主机 ASP.NET Core SignalR

通过[Brady Gaster](https://twitter.com/bradygaster)

本文提供了指南：

* 托管 SignalR 集线器使用宿主使用 ASP.NET Core 的后台工作进程。
* 将消息发送到连接从.NET Core 中的客户端[BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [（如何下载）](xref:index#how-to-download-a-sample)

## <a name="wire-up-signalr-during-startup"></a>在启动期间将 SignalR

在后台工作进程的上下文中承载 ASP.NET Core SignalR 集线器等同于承载的 ASP.NET Core web 应用中的中心。 在中`Startup.ConfigureServices`方法中，调用`services.AddSignalR`将所需的服务添加到 ASP.NET Core 依赖关系注入 (DI) 层以支持 SignalR。 在中`Startup.Configure`，则`UseSignalR`调用方法到 ASP.NET Core 请求管道中的中心终结点关联。

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

在前面的示例中，`ClockHub`类实现`Hub<T>`类，以创建强类型化的中心。 `ClockHub`已在中配置`Startup`类，以响应在终结点请求`/hubs/clock`。

强类型化的中心的详细信息，请参阅[适用于 ASP.NET Core SignalR 中使用中心](xref:signalr/hubs#strongly-typed-hubs)。

> [!NOTE]
> 此功能并不局限于[集线器\<T >](xref:Microsoft.AspNetCore.SignalR.Hub`1)类。 任何类都继承自[集线器](xref:Microsoft.AspNetCore.SignalR.Hub)，如[DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)，也将起作用。

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

使用强类型化的接口`ClockHub`是`IClock`接口。

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>从后台服务调用 SignalR Hub

启动过程中，`Worker`类， `BackgroundService`，使用有线`AddHostedService`。

```csharp
services.AddHostedService<Worker>();
```

由于 SignalR 还有线期间`Startup`阶段时，在其中每个中心已附加到 ASP.NET Core 的 HTTP 请求管道中的单个终结点，由表示每个中心`IHubContext<T>`在服务器上。 使用 ASP.NET Core DI 功能，如由托管层实例化其他类`BackgroundService`类、 MVC 控制器类或 Razor 页面模型，可以通过接受的实例来获取对服务器端集线器的引用`IHubContext<ClockHub, IClock>`在构造期间。

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

作为`ExecuteAsync`以迭代方式调用方法时在后台服务，服务器的当前日期和时间发送到连接的客户端使用`ClockHub`。

## <a name="react-to-signalr-events-with-background-services"></a>对使用后台服务 SignalR 事件做出响应

像使用 JavaScript 客户端 SignalR 或.NET 桌面应用程序可以执行的单页面应用程序使用的 using <xref:signalr/dotnet-client>、 一个`BackgroundService`或`IHostedService`实现还可用来连接到 SignalR 集线器和响应事件。

`ClockHubClient`类可实现`IClock`界面和`IHostedService`接口。 它可以建立连接，在这种方式`Startup`连续运行并响应来自服务器的中心事件。 

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

在初始化期间`ClockHubClient`创建的实例`HubConnection`和绑定`IClock.ShowTime`方法的处理程序为中心的`ShowTime`事件。

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

在中`IHostedService.StartAsync`实现中，`HubConnection`以异步方式启动。

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

期间`IHostedService.StopAsync`方法，`HubConnection`以异步方式释放。

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>其他资源

* [入门](xref:tutorials/signalr)
* [中心](xref:signalr/hubs)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
* [强类型化的中心](xref:signalr/hubs#strongly-typed-hubs)
