---
title: ':::no-loc(SignalR)::: HubContext'
author: bradygaster
description: '了解如何使用 ASP.NET Core :::no-loc(SignalR)::: HubContext 服务向中心外部的客户端发送通知。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
- ':::no-loc(IHubContext):::'
uid: signalr/hubcontext
ms.openlocfilehash: 0b1940dc85634051e8a566c6859f51c130b69269
ms.sourcegitcommit: 1b7f2e1aabf43fa93b920cad36515d7336bfc2df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93066728"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="f4f31-103">从中心外发送消息</span><span class="sxs-lookup"><span data-stu-id="f4f31-103">Send messages from outside a hub</span></span>

<span data-ttu-id="f4f31-104">作者：[Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="f4f31-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="f4f31-105">:::no-loc(SignalR):::中心是将消息发送到连接到服务器的客户端的核心抽象 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="f4f31-105">The :::no-loc(SignalR)::: hub is the core abstraction for sending messages to clients connected to the :::no-loc(SignalR)::: server.</span></span> <span data-ttu-id="f4f31-106">还可以使用服务从应用程序中的其他位置发送消息 `:::no-loc(IHubContext):::` 。</span><span class="sxs-lookup"><span data-stu-id="f4f31-106">It's also possible to send messages from other places in your app using the `:::no-loc(IHubContext):::` service.</span></span> <span data-ttu-id="f4f31-107">本文介绍如何访问 :::no-loc(SignalR)::: `:::no-loc(IHubContext):::` 以将通知从中心外发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="f4f31-107">This article explains how to access a :::no-loc(SignalR)::: `:::no-loc(IHubContext):::` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="f4f31-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/)[（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f4f31-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-no-locihubcontext"></a><span data-ttu-id="f4f31-109">获取实例 :::no-loc(IHubContext):::</span><span class="sxs-lookup"><span data-stu-id="f4f31-109">Get an instance of :::no-loc(IHubContext):::</span></span>

<span data-ttu-id="f4f31-110">在 ASP.NET Core 中 :::no-loc(SignalR)::: ，可以 `:::no-loc(IHubContext):::` 通过依赖关系注入访问的实例。</span><span class="sxs-lookup"><span data-stu-id="f4f31-110">In ASP.NET Core :::no-loc(SignalR):::, you can access an instance of `:::no-loc(IHubContext):::` via dependency injection.</span></span> <span data-ttu-id="f4f31-111">可以将的实例注入 `:::no-loc(IHubContext):::` 控制器、中间件或其他 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="f4f31-111">You can inject an instance of `:::no-loc(IHubContext):::` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="f4f31-112">使用实例将消息发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="f4f31-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="f4f31-113">这不同于 ASP.NET 4.x :::no-loc(SignalR)::: ，后者使用 GlobalHost 提供对的访问 `:::no-loc(IHubContext):::` 。</span><span class="sxs-lookup"><span data-stu-id="f4f31-113">This differs from ASP.NET 4.x :::no-loc(SignalR)::: which used GlobalHost to provide access to the `:::no-loc(IHubContext):::`.</span></span> <span data-ttu-id="f4f31-114">ASP.NET Core 具有依赖关系注入框架，无需此全局单一实例。</span><span class="sxs-lookup"><span data-stu-id="f4f31-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-no-locihubcontext-in-a-controller"></a><span data-ttu-id="f4f31-115">:::no-loc(IHubContext):::在控制器中注入实例</span><span class="sxs-lookup"><span data-stu-id="f4f31-115">Inject an instance of :::no-loc(IHubContext)::: in a controller</span></span>

<span data-ttu-id="f4f31-116">您可以 `:::no-loc(IHubContext):::` 通过将实例添加到构造函数中，将实例注入控制器：</span><span class="sxs-lookup"><span data-stu-id="f4f31-116">You can inject an instance of `:::no-loc(IHubContext):::` into a controller by adding it to your constructor:</span></span>

[!code-csharp[:::no-loc(IHubContext):::](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="f4f31-117">现在，有权访问的实例 `:::no-loc(IHubContext):::` ，你可以调用中心方法，就像在中心内一样。</span><span class="sxs-lookup"><span data-stu-id="f4f31-117">Now, with access to an instance of `:::no-loc(IHubContext):::`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[:::no-loc(IHubContext):::](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-no-locihubcontext-in-middleware"></a><span data-ttu-id="f4f31-118">获取 :::no-loc(IHubContext)::: 中间件中的实例</span><span class="sxs-lookup"><span data-stu-id="f4f31-118">Get an instance of :::no-loc(IHubContext)::: in middleware</span></span>

<span data-ttu-id="f4f31-119">访问 `:::no-loc(IHubContext):::` 中间件管道中的，如下所示：</span><span class="sxs-lookup"><span data-stu-id="f4f31-119">Access the `:::no-loc(IHubContext):::` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<:::no-loc(IHubContext):::<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> <span data-ttu-id="f4f31-120">当从类的外部调用中心方法时 `Hub` ，没有与调用关联的调用方。</span><span class="sxs-lookup"><span data-stu-id="f4f31-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="f4f31-121">因此，没有对 `ConnectionId` 、和属性的访问权限 `Caller` `Others` 。</span><span class="sxs-lookup"><span data-stu-id="f4f31-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-no-locihubcontext-from-ihost"></a><span data-ttu-id="f4f31-122">从 IHost 获取的实例 :::no-loc(IHubContext):::</span><span class="sxs-lookup"><span data-stu-id="f4f31-122">Get an instance of :::no-loc(IHubContext)::: from IHost</span></span>

<span data-ttu-id="f4f31-123">`:::no-loc(IHubContext):::`从 web 主机访问可用于与 ASP.NET Core 以外的区域进行集成，例如，使用第三方依赖关系注入框架：</span><span class="sxs-lookup"><span data-stu-id="f4f31-123">Accessing an `:::no-loc(IHubContext):::` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(:::no-loc(IHubContext):::<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="f4f31-124">注入强类型 HubContext</span><span class="sxs-lookup"><span data-stu-id="f4f31-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="f4f31-125">若要注入强类型 HubContext，请确保中心继承自 `Hub<T>` 。</span><span class="sxs-lookup"><span data-stu-id="f4f31-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="f4f31-126">使用 `:::no-loc(IHubContext):::<THub, T>` 接口而不是插入它 `:::no-loc(IHubContext):::<THub>` 。</span><span class="sxs-lookup"><span data-stu-id="f4f31-126">Inject it using the `:::no-loc(IHubContext):::<THub, T>` interface rather than `:::no-loc(IHubContext):::<THub>`.</span></span>

```csharp
public class ChatController : Controller
{
    public :::no-loc(IHubContext):::<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(:::no-loc(IHubContext):::<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

<span data-ttu-id="f4f31-127">有关详细信息，请参阅 [强类型中心](xref:signalr/hubs#strongly-typed-hubs) 。</span><span class="sxs-lookup"><span data-stu-id="f4f31-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="f4f31-128">相关资源</span><span class="sxs-lookup"><span data-stu-id="f4f31-128">Related resources</span></span>

* [<span data-ttu-id="f4f31-129">入门</span><span class="sxs-lookup"><span data-stu-id="f4f31-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="f4f31-130">中心</span><span class="sxs-lookup"><span data-stu-id="f4f31-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f4f31-131">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="f4f31-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
