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
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>使用 ASP.NET Core MessagePack 中心协议中 SignalR

通过[brennan，第 Conroy](https://github.com/BrennanConroy)

本文假定读者熟悉中所涉及的主题[开始](xref:tutorials/signalr)。

## <a name="what-is-messagepack"></a>MessagePack 是什么？

[MessagePack](https://msgpack.org/index.html)是快速和精简的二进制序列化格式。 性能和带宽是一个问题，因为它将创建较小的消息相比时很有用[JSON](https://www.json.org/)。 因为它是以二进制格式，则消息是不可读，查看网络跟踪和日志，除非通过 MessagePack 分析器传递字节时。 SignalR 为 MessagePack 格式中，提供内置支持，并提供 Api，可用于要使用的客户端和服务器。

## <a name="configure-messagepack-on-the-server"></a>在服务器上配置 MessagePack

若要启用 MessagePack 中心协议的服务器上，安装`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`应用程序中的包。 在 Startup.cs 文件中添加`AddMessagePackProtocol`到`AddSignalR`调用，以启用 MessagePack 支持在服务器上的。

> [!NOTE]
> 默认情况下启用 JSON。 添加 MessagePack，对 JSON 和 MessagePack 客户端的支持。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

若要自定义 MessagePack 设置你的数据的格式`AddMessagePackProtocol`接受委托，用于配置选项。 在该委托`FormatterResolvers`属性可以用于配置 MessagePack 序列化选项。 冲突解决程序的工作原理的详细信息，请访问位于的 MessagePack library [MessagePack CSharp](https://github.com/neuecc/MessagePack-CSharp)。 要序列化定义应如何处理它们的对象上，可以使用属性。

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

## <a name="configure-messagepack-on-the-client"></a>在客户端上配置 MessagePack

> [!NOTE]
> 支持的客户端的情况下，默认情况下启用 JSON。 客户端可以只支持单个协议。 添加 MessagePack 支持会将为任何以前配置的协议。

### <a name="net-client"></a>.NET 客户端

若要启用 MessagePack.NET 客户端中的，安装`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`包并调用`AddMessagePackProtocol`上`HubConnectionBuilder`。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 这`AddMessagePackProtocol`调用接受委托用于配置服务器一样的选项。

### <a name="javascript-client"></a>JavaScript 客户端

MessagePack 支持 Javascript 客户端提供的`@aspnet/signalr-protocol-msgpack`NPM 包。

```console
npm install @aspnet/signalr-protocol-msgpack
```

安装 npm 包之后, 该模块可以使用 JavaScript 模块加载程序通过直接或通过引用导入到浏览器 *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 文件。 在浏览器`msgpack5`还必须引用库。 使用`<script>`标记创建的引用。 可以在找到的库*node_modules\msgpack5\dist\msgpack5.js*。

> [!NOTE]
> 当使用`<script>`元素的顺序非常重要。 如果*signalr 协议 msgpack.js*引用之前*msgpack5.js*，尝试使用 MessagePack 连接时出错。 *signalr.js*也需要前*signalr 协议 msgpack.js*。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

添加`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`到`HubConnectionBuilder`将配置客户端连接到服务器时使用 MessagePack 协议。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 在此期间，有 JavaScript 客户端上的 MessagePack 协议没有配置选项。

## <a name="related-resources"></a>相关资源

* [入门](xref:tutorials/signalr)
* [.NET 客户端](xref:signalr/dotnet-client)
* [JavaScript 客户端](xref:signalr/javascript-client)
