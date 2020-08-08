---
title: SignalRHubContext
author: bradygaster
description: 了解如何使用 ASP.NET Core SignalR HubContext 服务向中心外部的客户端发送通知。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hubcontext
ms.openlocfilehash: b9adc54c1928d6ec11f707b2bd5e1e297973f1ae
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021921"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="734bb-103">从中心外发送消息</span><span class="sxs-lookup"><span data-stu-id="734bb-103">Send messages from outside a hub</span></span>

<span data-ttu-id="734bb-104">作者：[Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="734bb-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="734bb-105">SignalR中心是将消息发送到连接到服务器的客户端的核心抽象 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="734bb-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="734bb-106">还可以使用服务从应用程序中的其他位置发送消息 `IHubContext` 。</span><span class="sxs-lookup"><span data-stu-id="734bb-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="734bb-107">本文介绍如何访问 SignalR `IHubContext` 以将通知从中心外发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="734bb-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="734bb-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/)[（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="734bb-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-ihubcontext"></a><span data-ttu-id="734bb-109">获取 IHubContext 的实例</span><span class="sxs-lookup"><span data-stu-id="734bb-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="734bb-110">在 ASP.NET Core 中 SignalR ，可以 `IHubContext` 通过依赖关系注入访问的实例。</span><span class="sxs-lookup"><span data-stu-id="734bb-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="734bb-111">可以将的实例注入 `IHubContext` 控制器、中间件或其他 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="734bb-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="734bb-112">使用实例将消息发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="734bb-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="734bb-113">这不同于 ASP.NET 4.x SignalR ，后者使用 GlobalHost 提供对的访问 `IHubContext` 。</span><span class="sxs-lookup"><span data-stu-id="734bb-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="734bb-114">ASP.NET Core 具有依赖关系注入框架，无需此全局单一实例。</span><span class="sxs-lookup"><span data-stu-id="734bb-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a><span data-ttu-id="734bb-115">在控制器中注入 IHubContext 的实例</span><span class="sxs-lookup"><span data-stu-id="734bb-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="734bb-116">您可以 `IHubContext` 通过将实例添加到构造函数中，将实例注入控制器：</span><span class="sxs-lookup"><span data-stu-id="734bb-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="734bb-117">现在，有权访问的实例 `IHubContext` ，你可以调用中心方法，就像在中心内一样。</span><span class="sxs-lookup"><span data-stu-id="734bb-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a><span data-ttu-id="734bb-118">在中间件中获取 IHubContext 的实例</span><span class="sxs-lookup"><span data-stu-id="734bb-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="734bb-119">访问 `IHubContext` 中间件管道中的，如下所示：</span><span class="sxs-lookup"><span data-stu-id="734bb-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> <span data-ttu-id="734bb-120">当从类的外部调用中心方法时 `Hub` ，没有与调用关联的调用方。</span><span class="sxs-lookup"><span data-stu-id="734bb-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="734bb-121">因此，没有对 `ConnectionId` 、和属性的访问权限 `Caller` `Others` 。</span><span class="sxs-lookup"><span data-stu-id="734bb-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a><span data-ttu-id="734bb-122">从 IHost 获取 IHubContext 的实例</span><span class="sxs-lookup"><span data-stu-id="734bb-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="734bb-123">`IHubContext`从 web 主机访问可用于与 ASP.NET Core 以外的区域进行集成，例如，使用第三方依赖关系注入框架：</span><span class="sxs-lookup"><span data-stu-id="734bb-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="734bb-124">注入强类型 HubContext</span><span class="sxs-lookup"><span data-stu-id="734bb-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="734bb-125">若要注入强类型 HubContext，请确保中心继承自 `Hub<T>` 。</span><span class="sxs-lookup"><span data-stu-id="734bb-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="734bb-126">使用 `IHubContext<THub, T>` 接口而不是插入它 `IHubContext<THub>` 。</span><span class="sxs-lookup"><span data-stu-id="734bb-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

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

## <a name="related-resources"></a><span data-ttu-id="734bb-127">相关资源</span><span class="sxs-lookup"><span data-stu-id="734bb-127">Related resources</span></span>

* [<span data-ttu-id="734bb-128">入门</span><span class="sxs-lookup"><span data-stu-id="734bb-128">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="734bb-129">集线器</span><span class="sxs-lookup"><span data-stu-id="734bb-129">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="734bb-130">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="734bb-130">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
