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
# <a name="host-aspnet-core-signalr-in-background-services"></a>在后台服务中宿主 ASP.NET Core SignalR

作者： [Brady Gaster](https://twitter.com/bradygaster)

本文提供以下内容的指导：

* 使用 ASP.NET Core 承载的后台工作进程承载 SignalR 中心。
* 从 .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)中将消息发送到已连接的客户端。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [（如何下载）](xref:index#how-to-download-a-sample)

## <a name="wire-up-signalr-during-startup"></a>启动时连接 SignalR

::: moniker range=">= aspnetcore-3.0"

在后台工作进程的上下文中托管 ASP.NET Core SignalR 中心与在 ASP.NET Core 的 web 应用中托管集线器完全相同。 在方法中，调用`services.AddSignalR`会将所需服务添加到 ASP.NET Core 依赖关系注入（DI）层，以支持 SignalR。 `Startup.ConfigureServices` 在`Startup.Configure`中`MapHub` ，调用`UseEndpoints`方法以在 ASP.NET Core 请求管道中连接中心终结点。

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

在后台工作进程的上下文中托管 ASP.NET Core SignalR 中心与在 ASP.NET Core 的 web 应用中托管集线器完全相同。 在方法中，调用`services.AddSignalR`会将所需服务添加到 ASP.NET Core 依赖关系注入（DI）层，以支持 SignalR。 `Startup.ConfigureServices` 在`Startup.Configure`中`UseSignalR` ，调用方法以连接 ASP.NET Core 请求管道中的中心终结点。

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

在前面的示例中， `ClockHub`类`Hub<T>`实现类以创建强类型中心。 已在类中配置，以响应终结点`/hubs/clock`上的请求。 `Startup` `ClockHub`

有关强类型化集线器的详细信息，请参阅[在 SignalR 中使用集线器进行 ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs)。

> [!NOTE]
> 此功能并不限于[\<中心 t >](xref:Microsoft.AspNetCore.SignalR.Hub`1)类。 从[中心](xref:Microsoft.AspNetCore.SignalR.Hub)继承的任何类（如[DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)）也将起作用。

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

强类型化`ClockHub`所使用的接口`IClock`是接口。

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>从后台服务调用 SignalR 集线器

在启动过程中`Worker` ，类 a `BackgroundService`使用`AddHostedService`连接。

```csharp
services.AddHostedService<Worker>();
```

由于 SignalR 还`Startup`会在阶段中连接，其中每个集线器都附加到 ASP.NET Core 的 HTTP 请求管道中的单个终结点，因此，每个中心都`IHubContext<T>`由服务器上的表示。 使用 ASP.NET Core 的 DI 功能，由宿主层实例化的其他类（ `BackgroundService`如类、MVC 控制器类或 Razor 页面模型）可以通过`IHubContext<ClockHub, IClock>`在构造过程中接受实例来获取对服务器端集线器的引用。

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

由于在后台服务中以迭代`ClockHub`方式调用方法，服务器的当前日期和时间将使用发送到已连接的客户端。`ExecuteAsync`

## <a name="react-to-signalr-events-with-background-services"></a>通过后台服务响应 SignalR 事件

与使用适用于 SignalR 的 JavaScript 客户端的单页面应用程序或 .net 桌面应用程序<xref:signalr/dotnet-client>一样`BackgroundService` ，也可以使用、 `IHostedService`或实现来连接到 SignalR 集线器并对事件做出响应。

类实现`IClock`接口和`IHostedService`接口。 `ClockHubClient` 这样一来，就可以在运行`Startup`时将其连接起来，并响应服务器的集线器事件。

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

在初始化期间， `ClockHubClient`会创建一个`HubConnection`实例，并将该`IClock.ShowTime`方法与中心的`ShowTime`事件的处理程序进行连线。

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

在实现中`HubConnection` ，以异步方式启动。 `IHostedService.StartAsync`

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

在方法中`HubConnection` ，将异步释放。 `IHostedService.StopAsync`

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>其他资源

* [入门](xref:tutorials/signalr)
* [中心](xref:signalr/hubs)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
* [强类型中心](xref:signalr/hubs#strongly-typed-hubs)
