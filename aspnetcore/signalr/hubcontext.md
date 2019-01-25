---
title: SignalR HubContext
author: bradygaster
description: 了解如何使用 ASP.NET Core SignalR HubContext 服务用于向外部的客户端从一个中心发送通知。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2018
uid: signalr/hubcontext
ms.openlocfilehash: 73cf2c9d30ed5e409a75827fdab1f22b20427884
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837931"
---
# <a name="send-messages-from-outside-a-hub"></a>从向外发送邮件中心

通过[Mikael Mengistu](https://twitter.com/MikaelM_12)

SignalR 中心是用于将消息发送到客户端连接到 SignalR 服务器的核心抽象。 还有可能将消息从你的应用使用中的其他位置发送`IHubContext`服务。 此文章介绍了如何访问 SignalR`IHubContext`将通知发送到外部的客户端从一个中心。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [（如何下载）](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>获取 IHubContext 的实例

在 ASP.NET Core SignalR，您可以访问的实例`IHubContext`通过依赖关系注入。 您可以注入的实例`IHubContext`到控制器、 中间件或其他 DI 服务。 使用的实例将消息发送到客户端。

> [!NOTE]
> 这不同于 ASP.NET 4.x SignalR GlobalHost 用于提供对访问`IHubContext`。 ASP.NET Core具有的依赖关系注入框架，无需此全局单一实例。

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>注入控制器中的 IHubContext 实例

您可以注入的实例`IHubContext`插入控制器将其添加到您的构造函数：

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

现在，有权访问的实例`IHubContext`，好像您在中心本身中，可以调用集线器方法。

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>获取中间件中的 IHubContext 实例

访问`IHubContext`中间件管道中如下所示：

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<MyHub>>();
    //...
});
```

> [!NOTE]
> 外部的从调用集线器方法时`Hub`类中，没有与调用相关联的任何调用方。 因此，没有不能访问`ConnectionId`， `Caller`，和`Others`属性。

### <a name="inject-a-strongly-typed-hubcontext"></a>注入强类型化 HubContext

若要注入强类型化 HubContext，请确保你的中心继承`Hub<T>`。 将使用其注入`IHubContext<THub, T>`接口而非`IHubContext<THub>`。

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(message);
    }
}
```

## <a name="related-resources"></a>相关资源

* [入门](xref:tutorials/signalr)
* [中心](xref:signalr/hubs)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
