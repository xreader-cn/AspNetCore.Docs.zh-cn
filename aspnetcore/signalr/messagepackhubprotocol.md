---
title: 使用 ASP.NET Core MessagePack 中心协议中 SignalR
author: bradygaster
description: 将 MessagePack 中心协议添加到 ASP.NET Core SignalR。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/27/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7742f6f8bb53fb3c299ff98ae52a0da519ff396c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896514"
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

MessagePack 支持 JavaScript 客户端提供的`@aspnet/signalr-protocol-msgpack`npm 包。

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

## <a name="messagepack-quirks"></a>MessagePack quirks

有几个问题需要注意的使用 MessagePack 中心协议时。

### <a name="messagepack-is-case-sensitive"></a>MessagePack 是区分大小写

MessagePack 协议是区分大小写。 例如，请考虑以下C#类：

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

在从 JavaScript 客户端发送时，必须使用`PascalCased`属性名称，由于大小写必须与匹配C#完全类。 例如：

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

使用`camelCased`名称不会正确地绑定到C#类。 可以通过使用解决此`Key`属性来指定 MessagePack 属性不同的名称。 有关详细信息，请参阅[MessagePack CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>序列化/反序列化时，不会保留 DateTime.Kind

MessagePack 协议不提供方法来进行编码`Kind`的值`DateTime`。 因此，当反序列化日期，MessagePack 中心协议假定传入日期均采用 UTC 格式。 如果您正在使用`DateTime`本地时间中的值，我们建议将它们发送之前将转换为 UTC。 它们从 UTC 转换为本地时间时你收到它们。

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR # 2632年](https://github.com/aspnet/SignalR/issues/2632)。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>MessagePack 在 JavaScript 中不支持 DateTime.MinValue

[Msgpack5](https://github.com/mcollina/msgpack5)使用 SignalR JavaScript 客户端库不支持`timestamp96`MessagePack 中的类型。 此类型用于编码非常大的日期值 （无论是很早在过去或将来很久）。 值`DateTime.MinValue`是`January 1, 0001`其必须以编码`timestamp96`值。 由于此问题，发送`DateTime.MinValue`向 JavaScript 客户端不支持。 当`DateTime.MinValue`收到通过 JavaScript 客户端，引发以下错误：

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常情况下，`DateTime.MinValue`用于编码"缺少"或`null`值。 如果你需要对 MessagePack 中的值进行编码，使用一个可以为 null`DateTime`值 (`DateTime?`) 或编码单独`bool`值，该值指示日期是否存在。

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR # 2228年](https://github.com/aspnet/SignalR/issues/2228)。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>在"预时间的"编译环境中的 MessagePack 支持

[MessagePack CSharp](https://github.com/neuecc/MessagePack-CSharp)使用.NET 客户端和服务器库使用代码生成来优化序列化。 因此，它不支持默认情况下，在使用"预时间的"编译 （如 Xamarin iOS 或 Unity） 的环境。 就可以在这些环境中使用 MessagePack，通过"预生成"的序列化程序/反序列化程序代码。 有关详细信息，请参阅[MessagePack CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports)。 一旦预生成序列化程序，可以注册它们使用传递给配置委托`AddMessagePackProtocol`:

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>类型检查都在 MessagePack 更严格

JSON 中心协议将在反序列化期间执行类型转换。 例如，如果传入的对象的属性值是一个数字 (`{ foo: 42 }`) 上的.NET 类的属性属于类型，但`string`，值将被转换。 但是，MessagePack 不会执行此转换，并将引发异常，可以看到，在服务器端日志中 （并在控制台中）：

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR # 2937年](https://github.com/aspnet/SignalR/issues/2937)。

## <a name="related-resources"></a>相关资源

* [入门](xref:tutorials/signalr)
* [.NET 客户端](xref:signalr/dotnet-client)
* [JavaScript 客户端](xref:signalr/javascript-client)
