---
title: 使用 ASP.NET Core MessagePack 中心协议中 SignalR
author: bradygaster
description: 将 MessagePack 中心协议添加到 ASP.NET Core SignalR。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 2852ca93c62e706e9a5203625822c2fb954fd2b8
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835604"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="48b0a-103">使用 ASP.NET Core MessagePack 中心协议中 SignalR</span><span class="sxs-lookup"><span data-stu-id="48b0a-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="48b0a-104">通过[brennan，第 Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="48b0a-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="48b0a-105">本文假定读者熟悉中所涉及的主题[开始](xref:tutorials/signalr)。</span><span class="sxs-lookup"><span data-stu-id="48b0a-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="48b0a-106">MessagePack 是什么？</span><span class="sxs-lookup"><span data-stu-id="48b0a-106">What is MessagePack?</span></span>

<span data-ttu-id="48b0a-107">[MessagePack](https://msgpack.org/index.html)是快速和精简的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="48b0a-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="48b0a-108">性能和带宽是一个问题，因为它将创建较小的消息相比时很有用[JSON](https://www.json.org/)。</span><span class="sxs-lookup"><span data-stu-id="48b0a-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="48b0a-109">因为它是以二进制格式，则消息是不可读，查看网络跟踪和日志，除非通过 MessagePack 分析器传递字节时。</span><span class="sxs-lookup"><span data-stu-id="48b0a-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="48b0a-110">SignalR 为 MessagePack 格式中，提供内置支持，并提供 Api，可用于要使用的客户端和服务器。</span><span class="sxs-lookup"><span data-stu-id="48b0a-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="48b0a-111">在服务器上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="48b0a-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="48b0a-112">若要启用 MessagePack 中心协议的服务器上，安装`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`应用程序中的包。</span><span class="sxs-lookup"><span data-stu-id="48b0a-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="48b0a-113">在 Startup.cs 文件中添加`AddMessagePackProtocol`到`AddSignalR`调用，以启用 MessagePack 支持在服务器上的。</span><span class="sxs-lookup"><span data-stu-id="48b0a-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="48b0a-114">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="48b0a-114">JSON is enabled by default.</span></span> <span data-ttu-id="48b0a-115">添加 MessagePack，对 JSON 和 MessagePack 客户端的支持。</span><span class="sxs-lookup"><span data-stu-id="48b0a-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="48b0a-116">若要自定义 MessagePack 设置你的数据的格式`AddMessagePackProtocol`接受委托，用于配置选项。</span><span class="sxs-lookup"><span data-stu-id="48b0a-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="48b0a-117">在该委托`FormatterResolvers`属性可以用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="48b0a-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="48b0a-118">冲突解决程序的工作原理的详细信息，请访问位于的 MessagePack library [MessagePack CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="48b0a-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="48b0a-119">要序列化定义应如何处理它们的对象上，可以使用属性。</span><span class="sxs-lookup"><span data-stu-id="48b0a-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="48b0a-120">在客户端上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="48b0a-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="48b0a-121">支持的客户端的情况下，默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="48b0a-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="48b0a-122">客户端可以只支持单个协议。</span><span class="sxs-lookup"><span data-stu-id="48b0a-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="48b0a-123">添加 MessagePack 支持会将为任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="48b0a-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="48b0a-124">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="48b0a-124">.NET client</span></span>

<span data-ttu-id="48b0a-125">若要启用 MessagePack.NET 客户端中的，安装`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`包并调用`AddMessagePackProtocol`上`HubConnectionBuilder`。</span><span class="sxs-lookup"><span data-stu-id="48b0a-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="48b0a-126">这`AddMessagePackProtocol`调用接受委托用于配置服务器一样的选项。</span><span class="sxs-lookup"><span data-stu-id="48b0a-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="48b0a-127">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="48b0a-127">JavaScript client</span></span>

<span data-ttu-id="48b0a-128">MessagePack 支持 Javascript 客户端提供的`@aspnet/signalr-protocol-msgpack`NPM 包。</span><span class="sxs-lookup"><span data-stu-id="48b0a-128">MessagePack support for the Javascript client is provided by the `@aspnet/signalr-protocol-msgpack` NPM package.</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="48b0a-129">安装 npm 包之后, 该模块可以使用 JavaScript 模块加载程序通过直接或通过引用导入到浏览器 *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 文件。</span><span class="sxs-lookup"><span data-stu-id="48b0a-129">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* file.</span></span> <span data-ttu-id="48b0a-130">在浏览器`msgpack5`还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="48b0a-130">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="48b0a-131">使用`<script>`标记创建的引用。</span><span class="sxs-lookup"><span data-stu-id="48b0a-131">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="48b0a-132">可以在找到的库*node_modules\msgpack5\dist\msgpack5.js*。</span><span class="sxs-lookup"><span data-stu-id="48b0a-132">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="48b0a-133">当使用`<script>`元素的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="48b0a-133">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="48b0a-134">如果*signalr 协议 msgpack.js*引用之前*msgpack5.js*，尝试使用 MessagePack 连接时出错。</span><span class="sxs-lookup"><span data-stu-id="48b0a-134">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="48b0a-135">*signalr.js*也需要前*signalr 协议 msgpack.js*。</span><span class="sxs-lookup"><span data-stu-id="48b0a-135">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="48b0a-136">添加`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`到`HubConnectionBuilder`将配置客户端连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="48b0a-136">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="48b0a-137">在此期间，有 JavaScript 客户端上的 MessagePack 协议没有配置选项。</span><span class="sxs-lookup"><span data-stu-id="48b0a-137">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="48b0a-138">相关资源</span><span class="sxs-lookup"><span data-stu-id="48b0a-138">Related resources</span></span>

* [<span data-ttu-id="48b0a-139">入门</span><span class="sxs-lookup"><span data-stu-id="48b0a-139">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="48b0a-140">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="48b0a-140">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="48b0a-141">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="48b0a-141">JavaScript client</span></span>](xref:signalr/javascript-client)
