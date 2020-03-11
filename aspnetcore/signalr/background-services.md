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
ms.openlocfilehash: 86319cc93febab18c29e2fb6366cef0d025943ba
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651582"
---
# <a name="host-aspnet-core-opno-locsignalr-in-background-services"></a>在后台服务中宿主 ASP.NET Core SignalR

作者： [Brady Gaster](https://twitter.com/bradygaster)

本文提供以下内容的指导：

* 使用托管 ASP.NET Core 的后台工作进程承载 SignalR 中心。
* 从 .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)中将消息发送到已连接的客户端。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [（如何下载）](xref:index#how-to-download-a-sample)

## <a name="enable-opno-locsignalr-in-startup"></a>启用启动中的 SignalR

::: moniker range=">= aspnetcore-3.0"

在后台工作进程的上下文中托管 ASP.NET Core SignalR 中心与在 ASP.NET Core web 应用中托管集线器完全相同。 在 `Startup.ConfigureServices` 方法中，调用 `services.AddSignalR` 会将所需的服务添加到 ASP.NET Core 依赖关系注入（DI）层以支持 SignalR。 在 `Startup.Configure`中，`MapHub` 方法在 `UseEndpoints` 回调中调用，以连接 ASP.NET Core 请求管道中的中心终结点。

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

在后台工作进程的上下文中托管 ASP.NET Core SignalR 中心与在 ASP.NET Core web 应用中托管集线器完全相同。 在 `Startup.ConfigureServices` 方法中，调用 `services.AddSignalR` 会将所需的服务添加到 ASP.NET Core 依赖关系注入（DI）层以支持 SignalR。 在 `Startup.Configure`中，调用 `UseSignalR` 方法连接 ASP.NET Core 请求管道中的中心终结点。

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

在前面的示例中，`ClockHub` 类实现了 `Hub<T>` 类，以创建强类型化的中心。 已在 `Startup` 类中配置 `ClockHub`，以响应终结点 `/hubs/clock`的请求。

有关强类型化集线器的详细信息，请参阅[在 SignalR 中使用中心 ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs)。

> [!NOTE]
> 此功能并不限于[集线器\<t >](xref:Microsoft.AspNetCore.SignalR.Hub`1)类。 从[中心](xref:Microsoft.AspNetCore.SignalR.Hub)继承的任何类（如[DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)）也将起作用。

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

强类型 `ClockHub` 所使用的接口是 `IClock` 接口。

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-opno-locsignalr-hub-from-a-background-service"></a>从后台服务调用 SignalR 集线器

在启动过程中，将使用 `AddHostedService`启用 `Worker` 类 `BackgroundService`。

```csharp
services.AddHostedService<Worker>();
```

由于 SignalR 也是在 `Startup` 阶段启用的，在这种情况下，每个中心均附加到 ASP.NET Core 的 HTTP 请求管道中的单个终结点，每个中心由服务器上的 `IHubContext<T>` 表示。 使用 ASP.NET Core 的 DI 功能，由宿主层实例化的其他类（如 `BackgroundService` 类、MVC 控制器类或 Razor 页面模型）可通过在构造期间接受 `IHubContext<ClockHub, IClock>` 的实例来获取对服务器端集线器的引用。

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

由于在后台服务中以迭代方式调用 `ExecuteAsync` 方法，因此使用 `ClockHub`将服务器的当前日期和时间发送到已连接的客户端。

## <a name="react-to-opno-locsignalr-events-with-background-services"></a>通过后台服务对 SignalR 事件做出反应

与使用 SignalR 的 JavaScript 客户端或 .NET 桌面应用程序一样，使用 JavaScript 客户端的单页面应用程序也可以使用 <xref:signalr/dotnet-client>，`BackgroundService` 或 `IHostedService` 实现也可用于连接到 SignalR 中心和响应事件。

`ClockHubClient` 类既实现了 `IClock` 接口，又实现了 `IHostedService` 接口。 这样，便可以在 `Startup` 期间启用此功能，以便连续运行和响应来自服务器的集线器事件。

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

在初始化期间，`ClockHubClient` 将创建一个 `HubConnection` 的实例，并将 `IClock.ShowTime` 方法启用为中心的 `ShowTime` 事件的处理程序。

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

在 `IHostedService.StartAsync` 实现中，`HubConnection` 以异步方式启动。

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

在 `IHostedService.StopAsync` 方法期间，`HubConnection` 将异步释放。

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>其他资源

* [入门](xref:tutorials/signalr)
* [中心](xref:signalr/hubs)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
* [强类型中心](xref:signalr/hubs#strongly-typed-hubs)
