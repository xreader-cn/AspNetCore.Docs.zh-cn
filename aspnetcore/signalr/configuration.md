---
title: ASP.NET Core SignalR 配置
author: bradygaster
description: 了解如何配置 ASP.NET Core SignalR 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/06/2018
uid: signalr/configuration
ms.openlocfilehash: 06e86921c65297e93dcd8954ba4983d1577bb615
ms.sourcegitcommit: ca5f03210bedc61c6639a734ae5674bfe095dee8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2019
ms.locfileid: "55073148"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR 配置

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack 序列化选项

ASP.NET Core SignalR 支持使用两个协议进行消息编码：[JSON](https://www.json.org/)并[MessagePack](https://msgpack.org/index.html)。 每个协议具有序列化配置选项。

JSON 序列化可以配置服务器使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法，可以添加后[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)在你`Startup.ConfigureServices`方法。 `AddJsonProtocol`方法采用一个委托，接收`options`对象。 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)对该对象的属性是 JSON.NET`JsonSerializerSettings`可用于配置序列化的参数和返回值的对象。 请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)的更多详细信息。

例如，若要配置的序列化程序，而不是默认的"驼峰式大小写"名称，使用"pascal 命名法"属性名称，使用以下代码：

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
        new DefaultContractResolver();
    });
```

在.NET 客户端，相同`AddJsonProtocol`上存在扩展方法[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)。 `Microsoft.Extensions.DependencyInjection`必须导入命名空间，若要解决的扩展方法：

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> 不能在这一次配置 JavaScript 客户端中的 JSON 序列化。

### <a name="messagepack-serialization-options"></a>MessagePack 序列化选项

可以通过提供委托，配置 MessagePack 序列化[AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用。 请参阅[SignalR 中的 MessagePack](xref:signalr/messagepackhubprotocol)的更多详细信息。

> [!NOTE]
> 不能在此时间在 JavaScript 客户端中配置 MessagePack 序列化。

## <a name="configure-server-options"></a>配置服务器选项

下表描述了用于配置 SignalR 集线器的选项：

| 选项 | 默认值 | 描述 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | 服务器将会考虑客户端断开连接，如果它未在此时间间隔内收到一条消息 （包括保持活动状态）。 很可能会花费的时间超过客户端实际上要标记为已断开连接，由于这如何实现此超时间隔。 建议的值是双精度`KeepAliveInterval`值。|
| `HandshakeTimeout` | 15 秒 | 如果客户端不会在此时间间隔内发送初始握手消息，该连接已关闭。 这是一种高级的设置，如果由于出现严重的网络延迟发生握手超时错误应仅修改。 握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 如果服务器尚未在此时间间隔内发送一条消息，是自动发送一条 ping 消息使连接保持打开状态。 更改时`KeepAliveInterval`，更改`ServerTimeout` / `serverTimeoutInMilliseconds`设置在客户端上。 推荐`ServerTimeout` / `serverTimeoutInMilliseconds`值是双精度`KeepAliveInterval`值。  |
| `SupportedProtocols` | 所有已安装的协议 | 此中心支持的协议。 默认情况下，允许在服务器上注册的所有协议，但可以从禁用特定协议的单个中心此列表中删除协议。 |
| `EnableDetailedErrors` | `false` | 如果`true`、 详细异常消息返回到客户端集线器方法中引发异常。 默认值是`false`，因为这些异常消息可能包含敏感信息。 |

可以通过提供一个选项委托到的所有中心配置选项`AddSignalR`调用中`Startup.ConfigureServices`。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

有关单个集线器的选项重写中提供的全局选项`AddSignalR`，可以使用配置[AddHubOptions\<T >](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

使用`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级的设置。 通过将传递委托，配置这些选项[MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)。

| 选项 | 默认值 | 描述 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | 从客户端接收的字节数最大数量的服务器缓冲区。 增加此值允许服务器以接收更大的消息，但会降低内存占用情况。 |
| `AuthorizationData` | 从自动收集数据`Authorize`应用于集线器类的特性。 | 一系列[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象用于确定客户端有权连接到中心。 |
| `TransportMaxBufferSize` | 32 KB | 最大的应用发送的字节数的服务器缓冲区。 增加此值允许服务器以发送较大的消息，但会降低内存占用情况。 |
| `Transports` | 启用所有传输。 | 一个位掩码的`HttpTransportType`值可用于限制传输客户端可用来连接。 |
| `LongPolling` | 请参阅下文。 | 特定于长轮询传输的其他选项。 |
| `WebSockets` | 请参阅下文。 | 其他选项特定于 Websocket 传输。 |

长轮询传输中包含其他选项，可以使用配置`LongPolling`属性：

| 选项 | 默认值 | 描述 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 秒 | 服务器等待要终止单个轮询请求之前发送到客户端的消息的最大时间量。 减小此值会导致客户端更频繁地发出新轮询请求。 |

WebSocket 传输中包含其他选项，可以使用配置`WebSockets`属性：

| 选项 | 默认值 | 描述 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | 在服务器关闭，如果客户端无法在此时间间隔内关闭后，连接将终止。 |
| `SubProtocolSelector` | `null` | 一个委托，它可以用来设置`Sec-WebSocket-Protocol`为自定义值的标头。 该委托接收作为输入客户端请求的值，并应返回所需的值。 |

## <a name="configure-client-options"></a>配置客户端选项

可以在配置客户端选项`HubConnectionBuilder`类型 （适用于.NET 和 JavaScript 客户端），以及`HubConnection`本身。

### <a name="configure-logging"></a>配置日志记录

使用.NET 客户端中配置日志记录`ConfigureLogging`方法。 可以在相同的方式注册日志记录提供程序和筛选器，因为它们是在服务器上。 请参阅[登录 ASP.NET Core](xref:fundamentals/logging/index) 文档以了解更多信息。

> [!NOTE]
> 若要注册日志记录提供程序，必须安装所需的包。 请参阅[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分有关的完整列表。

例如，若要启用控制台日志记录，安装`Microsoft.Extensions.Logging.Console`NuGet 包。 调用`AddConsole`扩展方法：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

在 JavaScript 客户端，类似`configureLogging`方法存在。 提供`LogLevel`值，该值指示要生成的日志消息的最小级别。 日志将写入浏览器控制台窗口。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 若要禁用完全日志记录，请指定`signalR.LogLevel.None`在`configureLogging`方法。

下面列出了可用于 JavaScript 客户端的日志级别。 将日志级别设置为下列值之一，则记录上的消息**或更高版本**该级别。

| 级别 | 描述 |
| ----- | ----------- |
| `None` | 未不记录任何消息。 |
| `Critical` | 表示在整个应用程序时失败的消息。 |
| `Error` | 表示在当前操作失败的消息。 |
| `Warning` | 表示非致命性问题的消息。 |
| `Information` | 信息性消息。 |
| `Debug` | 用于调试的诊断消息。 |
| `Trace` | 用于诊断特定问题非常详细的诊断消息。 |

### <a name="configure-allowed-transports"></a>配置允许的传输

可以在中配置的 SignalR 使用的传输`WithUrl`调用 (`withUrl`在 JavaScript 中)。 位 OR 运算的值`HttpTransportType`可以用于限制客户端仅使用指定的传输。 默认情况下启用所有传输。

例如，若要禁用服务器发送事件传输，但允许 Websocket 和长轮询连接：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

在 JavaScript 客户端，通过设置来配置传输`transport`字段上的选项对象提供给`withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>配置持有者身份验证

若要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项 (`accessTokenFactory`在 JavaScript 中) 指定函数返回的所需的访问令牌。 在.NET 客户端，此访问令牌作为传入 HTTP"持有者身份验证"令牌 (使用`Authorization`具有一种类型的标头`Bearer`)。 在 JavaScript 客户端，使用访问令牌作为持有者令牌，**除**在少数情况下，在其中浏览器 Api 限制应用 （具体而言，在服务器发送事件和 Websocket 请求） 的标头的能力。 在这些情况下，查询字符串值中提供的访问令牌`access_token`。

在.NET 客户端，`AccessTokenProvider`可以使用中的选项委托指定选项`WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

在 JavaScript 客户端，通过设置配置的访问令牌`accessTokenFactory`字段中的选项对象`withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>配置超时和保持活动状态的选项

还提供用于配置超时和保持活动状态的行为的其他选项`HubConnection`对象本身：

| .NET 选项 | JavaScript 选项 | 默认值 | 描述 |
| ----------- | ----------------- | ------------- | ----------- |
| `ServerTimeout` | `serverTimeoutInMilliseconds` | 30 秒 （30000 毫秒） | 服务器活动的超时时间。 如果服务器尚未在此时间间隔内发送一条消息，客户端会考虑服务器断开连接和触发器`Closed`事件 (`onclose`在 JavaScript 中)。 此值必须足够大，以便从服务器发送的 ping 消息**和**超时间隔内收到的客户端。 建议的值是一个数字至少两倍的服务器的`KeepAliveInterval`值，以允许 ping 到达的时间。 |
| `HandshakeTimeout` | 不可配置 | 15 秒 | 初始服务器握手的超时时间。 如果服务器不在此时间间隔内发送握手响应，客户端取消握手和触发器`Closed`事件 (`onclose`在 JavaScript 中)。 这是一种高级的设置，如果由于出现严重的网络延迟发生握手超时错误应仅修改。 握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |

在.NET 客户端，超时值指定为`TimeSpan`值。 在 JavaScript 客户端，超时值指定为一个数字，指示以毫秒为单位的持续时间。

### <a name="configure-additional-options"></a>配置其他选项

可以在配置其他选项`WithUrl`(`withUrl`在 JavaScript 中) 上的方法`HubConnectionBuilder`:

| .NET 选项 | JavaScript 选项 | 默认值 | 描述 |
| ----------- | ----------------- | ------------- | ----------- |
| `AccessTokenProvider` | `accessTokenFactory` | `null` | 返回一个字符串，作为持有者身份验证令牌的 HTTP 请求中提供的函数。 |
| `SkipNegotiation` | `skipNegotiation` | `false` | 将此设置为`true`跳过协商步骤。 **WebSockets 传输是唯一的已启用的传输时，才支持**。 使用 Azure SignalR 服务时，不能启用此设置。 |
| `ClientCertificates` | 不可配置 * | 空 | 若要发送请求进行身份验证的 TLS 证书的集合。 |
| `Cookies` | 不可配置 * | 空 | 要与每个 HTTP 请求一起发送的 HTTP cookie 的集合。 |
| `Credentials` | 不可配置 * | 空 | 要与每个 HTTP 请求一起发送的凭据。 |
| `CloseTimeout` | 不可配置 * | 5 秒 | 仅 Websocket。 最长时间之后关闭服务器以确认关闭请求等待客户端。 如果服务器不在此时间内收到结束时，客户端断开连接。 |
| `Headers` | 不可配置 * | 空 | 附加 HTTP 标头要与每个 HTTP 请求一起发送的字典。 |
| `HttpMessageHandlerFactory` | 不可配置 * | `null` | 一个委托，它可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求。 不用于 WebSocket 连接。 此委托必须返回一个非 null 值，并接收作为参数的默认值。 修改该默认值上的设置，返回它，或者返回一个新`HttpMessageHandler`实例。 **时替换处理程序请务必复制你想要保留从提供的处理程序的设置，否则，配置的选项 （例如 Cookie 和标头） 不会应用于新的处理程序。** |
| `Proxy` | 不可配置 * | `null` | 发送 HTTP 请求时要使用 HTTP 代理。 |
| `UseDefaultCredentials` | 不可配置 * | `false` | 设置此布尔值，若要发送的 HTTP 和 Websocket 请求的默认凭据。 这使使用 Windows 身份验证。 |
| `WebSocketConfiguration` | 不可配置 * | `null` | 一个委托，可用于配置更多的 WebSocket 选项。 接收的实例[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)可用于配置选项。 |

选项标有星号 （*） 不是可在 JavaScript 客户端，由于浏览器 Api 中的限制中配置的。

在.NET 客户端，这些选项可以修改选项委托提供给`WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

在 JavaScript 客户端，可以提供给 JavaScript 对象中提供这些选项`withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
