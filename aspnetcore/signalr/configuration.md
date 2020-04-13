---
title: ASP.NET核心SignalR配置
author: bradygaster
description: 了解如何配置ASP.NET核心SignalR应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223980"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR 配置

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/消息包序列化选项

ASP.NET核心信号R支持两种用于编码消息的协议[：JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。 每个协议都有序列化配置选项。

JSON 序列化可以使用[AddJson 协议](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置。 `AddJsonProtocol`可以在`Startup.ConfigureServices`[中添加SignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)后添加 。 该方法`AddJsonProtocol`采用接收`options`对象的委托。 该对象的[Payload 序列化器选项](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)属性是`System.Text.Json`<xref:System.Text.Json.JsonSerializerOptions>可用于配置参数和返回值序列化的对象。 有关详细信息，请参阅[系统.Text.Json 文档](/dotnet/api/system.text.json)。

例如，要将序列化程序配置为不更改属性名称的大小写，而不是默认的"camelCase"名称，请使用 中的`Startup.ConfigureServices`以下代码：

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

在 .NET 客户端中`AddJsonProtocol`[，HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。 必须`Microsoft.Extensions.DependencyInjection`导入命名空间以解决扩展方法：

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> 此时无法在 JavaScript 客户端中配置 JSON 序列化。

### <a name="switch-to-newtonsoftjson"></a>切换到牛顿软. Json

如果您需要在 中不支持`Newtonsoft.Json`的功能`System.Text.Json`，请参阅[切换到 Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)。

### <a name="messagepack-serialization-options"></a>消息包序列化选项

消息包序列化可以通过提供对[AddMessagePack 协议](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用的委托来配置。 有关详细信息[，请参阅 SignalR 中的消息包](xref:signalr/messagepackhubprotocol)。

> [!NOTE]
> 此时无法在 JavaScript 客户端中配置 MessagePack 序列化。

## <a name="configure-server-options"></a>配置服务器选项

下表描述了用于配置 SignalR 集线器的选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | 如果客户端未在此间隔内收到消息（包括保持活动状态），服务器将考虑客户端断开连接。 由于如何实现，实际将客户端标记为断开连接的时间可能比此超时间隔长。 建议的值是值的`KeepAliveInterval`两倍。|
| `HandshakeTimeout` | 15 秒 | 如果客户端未在此时间间隔内发送初始握手消息，则连接将关闭。 这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。 有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 如果服务器未在此间隔内发送消息，则会自动发送 ping 消息以保持连接打开状态。 更改`KeepAliveInterval`时，更改`ServerTimeout`/`serverTimeoutInMilliseconds`客户端上的设置。 `ServerTimeout`/建议`serverTimeoutInMilliseconds`的值是值的`KeepAliveInterval`两倍。  |
| `SupportedProtocols` | 所有已安装的协议 | 此中心支持的协议。 默认情况下，允许在服务器上注册的所有协议，但可以从此列表中删除协议以禁用各个集线器的特定协议。 |
| `EnableDetailedErrors` | `false` | 如果在`true`Hub 方法中引发异常时，将返回到客户端的详细异常消息。 默认值为`false`，因为这些异常消息可能包含敏感信息。 |
| `StreamBufferCapacity` | `10` | 可缓冲客户端上载流的最大项数。 如果达到此限制，则在服务器处理流项之前将阻止调用的处理。|
| `MaximumReceiveMessageSize` | 32 KB | 单个传入中心消息的最大大小。 |

可以通过在 中`AddSignalR``Startup.ConfigureServices`提供委托给调用的选项，为所有集线器配置选项。

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

单个集线器的选项覆盖 中`AddSignalR`提供的全局选项，并且可以使用 配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>：

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>高级 HTTP 配置选项

用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。 这些选项通过将委托传递给[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)在`Startup.Configure`中配置。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

下表描述了用于配置ASP.NET核心信号R的高级 HTTP 选项的选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | 在应用背压之前，服务器缓冲的客户端接收的最大字节数。 增加此值允许服务器在不施加背压的情况下更快地接收更大的消息，但会增加内存消耗。 |
| `AuthorizationData` | 数据自动从应用于中心`Authorize`类的属性中收集。 | 用于确定客户端是否有权连接到集线器的[I授权数据](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。 |
| `TransportMaxBufferSize` | 32 KB | 在观察背压之前，服务器缓冲的应用发送的最大字节数。 增加此值允许服务器更快地缓冲较大的邮件，而无需等待回压，但会增加内存消耗。 |
| `Transports` | 所有传输都已启用。 | 位标记值枚举，`HttpTransportType`这些值可以限制客户端可用于连接的传输。 |
| `LongPolling` | 请参阅下文。 | 特定于长轮询传输的其他选项。 |
| `WebSockets` | 请参阅下文。 | 特定于 WebSocket 传输的其他选项。 |

长轮询传输具有可以使用 属性`LongPolling`配置的其他选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 秒 | 服务器在终止单个轮询请求之前等待消息发送到客户端的最大时间。 减小此值会导致客户端更频繁地发出新的轮询请求。 |

WebSocket 传输具有可以使用 属性`WebSockets`配置的其他选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | 服务器关闭后，如果客户端未能在此时间间隔内关闭，连接将终止。 |
| `SubProtocolSelector` | `null` | 可用于将`Sec-WebSocket-Protocol`标头设置为自定义值的委托。 委托接收客户端请求的值作为输入，并预期返回所需的值。 |

## <a name="configure-client-options"></a>配置客户端选项

可以在类型上`HubConnectionBuilder`配置客户端选项（在 .NET 和 JavaScript 客户端中可用）。 它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类包含生成器配置选项以及`HubConnection`本身。

### <a name="configure-logging"></a>配置日志记录

使用`ConfigureLogging`方法在 .NET 客户端中配置日志记录。 日志记录提供程序和筛选器的注册方式与在服务器上的注册方式相同。 有关详细信息[，请参阅登录ASP.NET核心](xref:fundamentals/logging/index)文档。

> [!NOTE]
> 要注册日志记录提供程序，必须安装必要的包。 有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。

例如，要启用控制台日志记录，`Microsoft.Extensions.Logging.Console`请安装 NuGet 包。 调用`AddConsole`扩展方法：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

在 JavaScript 客户端中，`configureLogging`存在类似的方法。 提供指示`LogLevel`要生产的日志消息的最小级别的值。 日志将写入浏览器控制台窗口。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

还可以提供表示`LogLevel`日志级别名称`string`的值，而不是值。 这在无法访问`LogLevel`常量的环境中配置 SignalR 日志记录时非常有用。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

下表列出了可用的日志级别。 您提供的值来`configureLogging`设置将记录的**最小**日志级别。 将记录在此级别记录的消息，**或表中列出的消息级别**。

| 字符串                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**或**`information` | `LogLevel.Information` |
| `warn`**或**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> 要完全禁用日志记录，请在`signalR.LogLevel.None``configureLogging`方法中指定。

有关日志记录的详细信息，请参阅[信号R 诊断文档](xref:signalr/diagnostics)。

SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。 它是一种高级日志记录 API，允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。 以下代码段演示如何与 SignalR `java.util.logging` Java 客户端一起使用。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

如果不在依赖项中配置日志记录，SLF4J 将加载具有以下警告消息的默认无操作记录器：

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

这可以安全地忽略。

### <a name="configure-allowed-transports"></a>配置允许的传输

SignalR 使用的传输可以在`WithUrl`调用中（`withUrl`在 JavaScript 中）进行配置。 值`HttpTransportType`的位 OR 可用于限制客户端仅使用指定的传输。 默认情况下，所有传输都已启用。

例如，要禁用服务器发送事件传输，但允许 WebSocket 和长轮询连接：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

在 JavaScript 客户端中，通过设置提供给 的选项`transport`对象上的字段来`withUrl`配置传输：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

在此版本中，Java 客户端 Websocket 是唯一可用的传输。

在 Java 客户端中，使用 上`withTransport`的方法选择传输。 `HttpHubConnectionBuilder` Java 客户端默认使用 WebSocket 传输。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR Java 客户端还不支持传输回退。

### <a name="configure-bearer-authentication"></a>配置无记名身份验证

要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项`accessTokenFactory`（在 JavaScript 中）指定返回所需访问令牌的函数。 在 .NET 客户端中，此访问令牌作为 HTTP"承载身份验证"令牌传入（使用`Authorization`具有 的`Bearer`标头的类型）。 在 JavaScript 客户端中，访问令牌用作承载令牌，**除非**浏览器 API 限制应用标头的能力（特别是在服务器发送事件和 WebSocket 请求中）。 在这些情况下，访问令牌作为查询字符串值`access_token`提供。

在 .NET 客户端中`AccessTokenProvider`，可以使用 中`WithUrl`的选项委托指定该选项：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

在 JavaScript 客户端中，通过设置 中`accessTokenFactory``withUrl`的选项对象上的字段来配置访问令牌。

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

在 SignalR Java 客户端中，可以通过向[HttpHubConnectBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置承载令牌以用于身份验证。 [与AccessTokenFactory一起](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava)[单\<字符串>。 ](https://reactivex.io/documentation/single.html) 调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来生成客户端的访问令牌。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>配置超时和保持活动选项

`HubConnection`用于配置超时和保持活动行为的其他选项在对象本身上可用：

# <a name="net"></a>[.NET](#tab/dotnet)

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 秒（30，000 毫秒） | 服务器活动的超时。 如果服务器未在此间隔内发送消息，则客户端将认为服务器已断开连接并触发`Closed`事件（`onclose`在 JavaScript 中）。 此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。 建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。 |
| `HandshakeTimeout` | 15 秒 | 初始服务器握手的超时。 如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`Closed`事件（`onclose`在 JavaScript 中）。 这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。 有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 确定客户端发送 ping 消息的时间间隔。 从客户端发送任何消息会将计时器重置为间隔的开始。 如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。 |

在 .NET 客户端中，超时值指定为`TimeSpan`值。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 秒（30，000 毫秒） | 服务器活动的超时。 如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onclose`事件。 此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。 建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。 |
| `keepAliveIntervalInMilliseconds` | 15 秒（15，000 毫秒） | 确定客户端发送 ping 消息的时间间隔。 从客户端发送任何消息会将计时器重置为间隔的开始。 如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。 |

# <a name="java"></a>[Java](#tab/java)

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 秒（30，000 毫秒） | 服务器活动的超时。 如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onClose`事件。 此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。 建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初始服务器握手的超时。 如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`onClose`事件。 这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。 有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 秒（15，000 毫秒） | 确定客户端发送 ping 消息的时间间隔。 从客户端发送任何消息会将计时器重置为间隔的开始。 如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。 |

---

### <a name="configure-additional-options"></a>配置其他选项

其他选项可以在 Java 客户端`WithUrl`上`withUrl`或 Java 客户端上`HubConnectionBuilder`的各种配置 API 上（JavaScript）方法`HttpHubConnectionBuilder`中配置：

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 选项 |  默认值 | 说明 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | 返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。 |
| `SkipNegotiation` | `false` | 将此`true`设置为跳过协商步骤。 **仅当 WebSocket 传输是唯一启用的传输时才受支持**。 使用 Azure SignalR 服务时无法启用此设置。 |
| `ClientCertificates` | 空 | 要发送到身份验证请求的 TLS 证书的集合。 |
| `Cookies` | 空 | 要随每个 HTTP 请求一起发送的 HTTP Cookie 集合。 |
| `Credentials` | 空 | 要随每个 HTTP 请求一起发送的凭据。 |
| `CloseTimeout` | 5 秒 | 仅限 Web 插座。 客户端关闭后等待服务器确认关闭请求的最大时间量。 如果服务器在此时间内未确认关闭，则客户端将断开连接。 |
| `Headers` | 空 | 要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。 |
| `HttpMessageHandlerFactory` | `null` | 可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求的委托。 不用于 Web 套接字连接。 此委托必须返回一个非空值，并且它将作为参数接收默认值。 修改该默认值上的设置并返回该设置，或返回新`HttpMessageHandler`实例。 **替换处理程序时，请确保从提供的处理程序复制要保留的设置，否则，配置的选项（如 Cookie 和标头）将不适用于新处理程序。** |
| `Proxy` | `null` | 发送 HTTP 请求时要使用的 HTTP 代理。 |
| `UseDefaultCredentials` | `false` | 设置此布尔以发送 HTTP 和 WebSocket 请求的默认凭据。 这支持使用 Windows 身份验证。 |
| `WebSocketConfiguration` | `null` | 可用于配置其他 WebSocket 选项的委托。 接收可用于配置选项的[客户端WebSocket选项](/dotnet/api/system.net.websockets.clientwebsocketoptions)的实例。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 选项 | 默认值 | 说明 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | 返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。 |
| `skipNegotiation` | `false` | 将此`true`设置为跳过协商步骤。 **仅当 WebSocket 传输是唯一启用的传输时才受支持**。 使用 Azure SignalR 服务时无法启用此设置。 |

# <a name="java"></a>[Java](#tab/java)

| Java 选项 | 默认值 | 说明 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | 返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。 |
| `shouldSkipNegotiate` | `false` | 将此`true`设置为跳过协商步骤。 **仅当 WebSocket 传输是唯一启用的传输时才受支持**。 使用 Azure SignalR 服务时无法启用此设置。 |
| `withHeader` `withHeaders` | 空 | 要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。 |

---

在 .NET 客户端中，这些选项可以通过提供给`WithUrl`： 提供的选项委托进行修改：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

在 JavaScript 客户端中，这些选项可以在提供给`withUrl`的 JavaScript 对象中提供：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

在 Java 客户端中，可以使用`HttpHubConnectionBuilder`从`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/消息包序列化选项

ASP.NET核心信号R支持两种用于编码消息的协议[：JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。 每个协议都有序列化配置选项。

JSON 序列化可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置，该方法可以在方法中的`Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。 该方法`AddJsonProtocol`采用接收`options`对象的委托。 该对象的[Payload 序列化器设置](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于`JsonSerializerSettings`配置参数和返回值序列化JSON.NET对象。 有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。
 
例如，要将序列化程序配置为使用"PascalCase"属性名称，而不是默认的"camelCase"名称，请使用 中的`Startup.ConfigureServices`以下代码：
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

在 .NET 客户端中`AddJsonProtocol`[，HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。 必须`Microsoft.Extensions.DependencyInjection`导入命名空间以解决扩展方法：

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
> 此时无法在 JavaScript 客户端中配置 JSON 序列化。

### <a name="messagepack-serialization-options"></a>消息包序列化选项

消息包序列化可以通过提供对[AddMessagePack 协议](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用的委托来配置。 有关详细信息[，请参阅 SignalR 中的消息包](xref:signalr/messagepackhubprotocol)。

> [!NOTE]
> 此时无法在 JavaScript 客户端中配置 MessagePack 序列化。

## <a name="configure-server-options"></a>配置服务器选项

下表描述了用于配置 SignalR 集线器的选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | 如果客户端未在此间隔内收到消息（包括保持活动状态），服务器将考虑客户端断开连接。 由于如何实现，实际将客户端标记为断开连接的时间可能比此超时间隔长。 建议的值是值的`KeepAliveInterval`两倍。|
| `HandshakeTimeout` | 15 秒 | 如果客户端未在此时间间隔内发送初始握手消息，则连接将关闭。 这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。 有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 如果服务器未在此间隔内发送消息，则会自动发送 ping 消息以保持连接打开状态。 更改`KeepAliveInterval`时，更改`ServerTimeout`/`serverTimeoutInMilliseconds`客户端上的设置。 `ServerTimeout`/建议`serverTimeoutInMilliseconds`的值是值的`KeepAliveInterval`两倍。  |
| `SupportedProtocols` | 所有已安装的协议 | 此中心支持的协议。 默认情况下，允许在服务器上注册的所有协议，但可以从此列表中删除协议以禁用各个集线器的特定协议。 |
| `EnableDetailedErrors` | `false` | 如果在`true`Hub 方法中引发异常时，将返回到客户端的详细异常消息。 默认值为`false`，因为这些异常消息可能包含敏感信息。 |

可以通过在 中`AddSignalR``Startup.ConfigureServices`提供委托给调用的选项，为所有集线器配置选项。

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

单个集线器的选项覆盖 中`AddSignalR`提供的全局选项，并且可以使用 配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>：

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>高级 HTTP 配置选项

用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。 这些选项通过将委托传递给[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)在`Startup.Configure`中配置。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

下表描述了用于配置ASP.NET核心信号R的高级 HTTP 选项的选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | 从服务器缓冲的客户端接收的最大字节数。 增加此值允许服务器接收较大的消息，但可能会对内存消耗产生负面影响。 |
| `AuthorizationData` | 数据自动从应用于中心`Authorize`类的属性中收集。 | 用于确定客户端是否有权连接到集线器的[I授权数据](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。 |
| `TransportMaxBufferSize` | 32 KB | 服务器缓冲的应用发送的最大字节数。 增加此值允许服务器发送更大的消息，但可能会对内存消耗产生负面影响。 |
| `Transports` | 所有传输都已启用。 | 位标记值枚举，`HttpTransportType`这些值可以限制客户端可用于连接的传输。 |
| `LongPolling` | 请参阅下文。 | 特定于长轮询传输的其他选项。 |
| `WebSockets` | 请参阅下文。 | 特定于 WebSocket 传输的其他选项。 |

长轮询传输具有可以使用 属性`LongPolling`配置的其他选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 秒 | 服务器在终止单个轮询请求之前等待消息发送到客户端的最大时间。 减小此值会导致客户端更频繁地发出新的轮询请求。 |

WebSocket 传输具有可以使用 属性`WebSockets`配置的其他选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | 服务器关闭后，如果客户端未能在此时间间隔内关闭，连接将终止。 |
| `SubProtocolSelector` | `null` | 可用于将`Sec-WebSocket-Protocol`标头设置为自定义值的委托。 委托接收客户端请求的值作为输入，并预期返回所需的值。 |

## <a name="configure-client-options"></a>配置客户端选项

可以在类型上`HubConnectionBuilder`配置客户端选项（在 .NET 和 JavaScript 客户端中可用）。 它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类包含生成器配置选项以及`HubConnection`本身。

### <a name="configure-logging"></a>配置日志记录

使用`ConfigureLogging`方法在 .NET 客户端中配置日志记录。 日志记录提供程序和筛选器的注册方式与在服务器上的注册方式相同。 有关详细信息[，请参阅登录ASP.NET核心](xref:fundamentals/logging/index)文档。

> [!NOTE]
> 要注册日志记录提供程序，必须安装必要的包。 有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。

例如，要启用控制台日志记录，`Microsoft.Extensions.Logging.Console`请安装 NuGet 包。 调用`AddConsole`扩展方法：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

在 JavaScript 客户端中，`configureLogging`存在类似的方法。 提供指示`LogLevel`要生产的日志消息的最小级别的值。 日志将写入浏览器控制台窗口。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 要完全禁用日志记录，请在`signalR.LogLevel.None``configureLogging`方法中指定。

有关日志记录的详细信息，请参阅[信号R 诊断文档](xref:signalr/diagnostics)。

SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。 它是一种高级日志记录 API，允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。 以下代码段演示如何与 SignalR `java.util.logging` Java 客户端一起使用。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

如果不在依赖项中配置日志记录，SLF4J 将加载具有以下警告消息的默认无操作记录器：

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

这可以安全地忽略。

### <a name="configure-allowed-transports"></a>配置允许的传输

SignalR 使用的传输可以在`WithUrl`调用中（`withUrl`在 JavaScript 中）进行配置。 值`HttpTransportType`的位 OR 可用于限制客户端仅使用指定的传输。 默认情况下，所有传输都已启用。

例如，要禁用服务器发送事件传输，但允许 WebSocket 和长轮询连接：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

在 JavaScript 客户端中，通过设置提供给 的选项`transport`对象上的字段来`withUrl`配置传输：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

在此版本中，Java 客户端 Websocket 是唯一可用的传输。

### <a name="configure-bearer-authentication"></a>配置无记名身份验证

要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项`accessTokenFactory`（在 JavaScript 中）指定返回所需访问令牌的函数。 在 .NET 客户端中，此访问令牌作为 HTTP"承载身份验证"令牌传入（使用`Authorization`具有 的`Bearer`标头的类型）。 在 JavaScript 客户端中，访问令牌用作承载令牌，**除非**浏览器 API 限制应用标头的能力（特别是在服务器发送事件和 WebSocket 请求中）。 在这些情况下，访问令牌作为查询字符串值`access_token`提供。

在 .NET 客户端中`AccessTokenProvider`，可以使用 中`WithUrl`的选项委托指定该选项：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

在 JavaScript 客户端中，通过设置 中`accessTokenFactory``withUrl`的选项对象上的字段来配置访问令牌。

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

在 SignalR Java 客户端中，可以通过向[HttpHubConnectBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置承载令牌以用于身份验证。 [与AccessTokenFactory一起](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava)[单\<字符串>。 ](https://reactivex.io/documentation/single.html) 调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来生成客户端的访问令牌。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>配置超时和保持活动选项

`HubConnection`用于配置超时和保持活动行为的其他选项在对象本身上可用：

# <a name="net"></a>[.NET](#tab/dotnet)

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 秒（30，000 毫秒） | 服务器活动的超时。 如果服务器未在此间隔内发送消息，则客户端将认为服务器已断开连接并触发`Closed`事件（`onclose`在 JavaScript 中）。 此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。 建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。 |
| `HandshakeTimeout` | 15 秒 | 初始服务器握手的超时。 如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`Closed`事件（`onclose`在 JavaScript 中）。 这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。 有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 确定客户端发送 ping 消息的时间间隔。 从客户端发送任何消息会将计时器重置为间隔的开始。 如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。 |

在 .NET 客户端中，超时值指定为`TimeSpan`值。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 秒（30，000 毫秒） | 服务器活动的超时。 如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onclose`事件。 此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。 建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。 |
| `keepAliveIntervalInMilliseconds` | 15 秒（15，000 毫秒） | 确定客户端发送 ping 消息的时间间隔。 从客户端发送任何消息会将计时器重置为间隔的开始。 如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。 |

# <a name="java"></a>[Java](#tab/java)

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 秒（30，000 毫秒） | 服务器活动的超时。 如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onClose`事件。 此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。 建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初始服务器握手的超时。 如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`onClose`事件。 这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。 有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 秒（15，000 毫秒） | 确定客户端发送 ping 消息的时间间隔。 从客户端发送任何消息会将计时器重置为间隔的开始。 如果客户端未在服务器上的`ClientTimeoutInterval`集中发送消息，则服务器将认为客户端已断开连接。 |

---

### <a name="configure-additional-options"></a>配置其他选项

其他选项可以在 Java 客户端`WithUrl`上`withUrl`或 Java 客户端上`HubConnectionBuilder`的各种配置 API 上（JavaScript）方法`HttpHubConnectionBuilder`中配置：

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 选项 |  默认值 | 说明 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | 返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。 |
| `SkipNegotiation` | `false` | 将此`true`设置为跳过协商步骤。 **仅当 WebSocket 传输是唯一启用的传输时才受支持**。 使用 Azure SignalR 服务时无法启用此设置。 |
| `ClientCertificates` | 空 | 要发送到身份验证请求的 TLS 证书的集合。 |
| `Cookies` | 空 | 要随每个 HTTP 请求一起发送的 HTTP Cookie 集合。 |
| `Credentials` | 空 | 要随每个 HTTP 请求一起发送的凭据。 |
| `CloseTimeout` | 5 秒 | 仅限 Web 插座。 客户端关闭后等待服务器确认关闭请求的最大时间量。 如果服务器在此时间内未确认关闭，则客户端将断开连接。 |
| `Headers` | 空 | 要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。 |
| `HttpMessageHandlerFactory` | `null` | 可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求的委托。 不用于 Web 套接字连接。 此委托必须返回一个非空值，并且它将作为参数接收默认值。 修改该默认值上的设置并返回该设置，或返回新`HttpMessageHandler`实例。 **替换处理程序时，请确保从提供的处理程序复制要保留的设置，否则，配置的选项（如 Cookie 和标头）将不适用于新处理程序。** |
| `Proxy` | `null` | 发送 HTTP 请求时要使用的 HTTP 代理。 |
| `UseDefaultCredentials` | `false` | 设置此布尔以发送 HTTP 和 WebSocket 请求的默认凭据。 这支持使用 Windows 身份验证。 |
| `WebSocketConfiguration` | `null` | 可用于配置其他 WebSocket 选项的委托。 接收可用于配置选项的[客户端WebSocket选项](/dotnet/api/system.net.websockets.clientwebsocketoptions)的实例。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 选项 | 默认值 | 说明 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | 返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。 |
| `skipNegotiation` | `false` | 将此`true`设置为跳过协商步骤。 **仅当 WebSocket 传输是唯一启用的传输时才受支持**。 使用 Azure SignalR 服务时无法启用此设置。 |

# <a name="java"></a>[Java](#tab/java)

| Java 选项 | 默认值 | 说明 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | 返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。 |
| `shouldSkipNegotiate` | `false` | 将此`true`设置为跳过协商步骤。 **仅当 WebSocket 传输是唯一启用的传输时才受支持**。 使用 Azure SignalR 服务时无法启用此设置。 |
| `withHeader` `withHeaders` | 空 | 要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。 |

---

在 .NET 客户端中，这些选项可以通过提供给`WithUrl`： 提供的选项委托进行修改：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

在 JavaScript 客户端中，这些选项可以在提供给`withUrl`的 JavaScript 对象中提供：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

在 Java 客户端中，可以使用`HttpHubConnectionBuilder`从`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/消息包序列化选项

ASP.NET核心信号R支持两种用于编码消息的协议[：JSON](https://www.json.org/)和[MessagePack](https://msgpack.org/index.html)。 每个协议都有序列化配置选项。

JSON 序列化可以使用[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)扩展方法在服务器上配置，该方法可以在方法中的`Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)之后添加。 该方法`AddJsonProtocol`采用接收`options`对象的委托。 该对象的[Payload 序列化器设置](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)属性是可用于`JsonSerializerSettings`配置参数和返回值序列化JSON.NET对象。 有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。
 
例如，要将序列化程序配置为使用"PascalCase"属性名称，而不是默认的"camelCase"名称，请使用 中的`Startup.ConfigureServices`以下代码：
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

在 .NET 客户端中`AddJsonProtocol`[，HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)上存在相同的扩展方法。 必须`Microsoft.Extensions.DependencyInjection`导入命名空间以解决扩展方法：

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
> 此时无法在 JavaScript 客户端中配置 JSON 序列化。

### <a name="messagepack-serialization-options"></a>消息包序列化选项

消息包序列化可以通过提供对[AddMessagePack 协议](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)调用的委托来配置。 有关详细信息[，请参阅 SignalR 中的消息包](xref:signalr/messagepackhubprotocol)。

> [!NOTE]
> 此时无法在 JavaScript 客户端中配置 MessagePack 序列化。

## <a name="configure-server-options"></a>配置服务器选项

下表描述了用于配置 SignalR 集线器的选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 秒 | 如果客户端未在此时间间隔内发送初始握手消息，则连接将关闭。 这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。 有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |
| `KeepAliveInterval` | 15 秒 | 如果服务器未在此间隔内发送消息，则会自动发送 ping 消息以保持连接打开状态。 更改`KeepAliveInterval`时，更改`ServerTimeout`/`serverTimeoutInMilliseconds`客户端上的设置。 `ServerTimeout`/建议`serverTimeoutInMilliseconds`的值是值的`KeepAliveInterval`两倍。  |
| `SupportedProtocols` | 所有已安装的协议 | 此中心支持的协议。 默认情况下，允许在服务器上注册的所有协议，但可以从此列表中删除协议以禁用各个集线器的特定协议。 |
| `EnableDetailedErrors` | `false` | 如果在`true`Hub 方法中引发异常时，将返回到客户端的详细异常消息。 默认值为`false`，因为这些异常消息可能包含敏感信息。 |

可以通过在 中`AddSignalR``Startup.ConfigureServices`提供委托给调用的选项，为所有集线器配置选项。

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

单个集线器的选项覆盖 中`AddSignalR`提供的全局选项，并且可以使用 配置<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>：

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>高级 HTTP 配置选项

用于`HttpConnectionDispatcherOptions`配置与传输和内存缓冲区管理相关的高级设置。 这些选项通过将委托传递给[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)在`Startup.Configure`中配置。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

下表描述了用于配置ASP.NET核心信号R的高级 HTTP 选项的选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | 从服务器缓冲的客户端接收的最大字节数。 增加此值允许服务器接收较大的消息，但可能会对内存消耗产生负面影响。 |
| `AuthorizationData` | 数据自动从应用于中心`Authorize`类的属性中收集。 | 用于确定客户端是否有权连接到集线器的[I授权数据](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)对象的列表。 |
| `TransportMaxBufferSize` | 32 KB | 服务器缓冲的应用发送的最大字节数。 增加此值允许服务器发送更大的消息，但可能会对内存消耗产生负面影响。 |
| `Transports` | 所有传输都已启用。 | 位标记值枚举，`HttpTransportType`这些值可以限制客户端可用于连接的传输。 |
| `LongPolling` | 请参阅下文。 | 特定于长轮询传输的其他选项。 |
| `WebSockets` | 请参阅下文。 | 特定于 WebSocket 传输的其他选项。 |

长轮询传输具有可以使用 属性`LongPolling`配置的其他选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 秒 | 服务器在终止单个轮询请求之前等待消息发送到客户端的最大时间。 减小此值会导致客户端更频繁地发出新的轮询请求。 |

WebSocket 传输具有可以使用 属性`WebSockets`配置的其他选项：

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | 服务器关闭后，如果客户端未能在此时间间隔内关闭，连接将终止。 |
| `SubProtocolSelector` | `null` | 可用于将`Sec-WebSocket-Protocol`标头设置为自定义值的委托。 委托接收客户端请求的值作为输入，并预期返回所需的值。 |

## <a name="configure-client-options"></a>配置客户端选项

可以在类型上`HubConnectionBuilder`配置客户端选项（在 .NET 和 JavaScript 客户端中可用）。 它在 Java 客户端中也可用，但`HttpHubConnectionBuilder`子类包含生成器配置选项以及`HubConnection`本身。

### <a name="configure-logging"></a>配置日志记录

使用`ConfigureLogging`方法在 .NET 客户端中配置日志记录。 日志记录提供程序和筛选器的注册方式与在服务器上的注册方式相同。 有关详细信息[，请参阅登录ASP.NET核心](xref:fundamentals/logging/index)文档。

> [!NOTE]
> 要注册日志记录提供程序，必须安装必要的包。 有关完整列表，请参阅文档的[内置日志记录提供程序](xref:fundamentals/logging/index#built-in-logging-providers)部分。

例如，要启用控制台日志记录，`Microsoft.Extensions.Logging.Console`请安装 NuGet 包。 调用`AddConsole`扩展方法：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

在 JavaScript 客户端中，`configureLogging`存在类似的方法。 提供指示`LogLevel`要生产的日志消息的最小级别的值。 日志将写入浏览器控制台窗口。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 要完全禁用日志记录，请在`signalR.LogLevel.None``configureLogging`方法中指定。

有关日志记录的详细信息，请参阅[信号R 诊断文档](xref:signalr/diagnostics)。

SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。 它是一种高级日志记录 API，允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。 以下代码段演示如何与 SignalR `java.util.logging` Java 客户端一起使用。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

如果不在依赖项中配置日志记录，SLF4J 将加载具有以下警告消息的默认无操作记录器：

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

这可以安全地忽略。

### <a name="configure-allowed-transports"></a>配置允许的传输

SignalR 使用的传输可以在`WithUrl`调用中（`withUrl`在 JavaScript 中）进行配置。 值`HttpTransportType`的位 OR 可用于限制客户端仅使用指定的传输。 默认情况下，所有传输都已启用。

例如，要禁用服务器发送事件传输，但允许 WebSocket 和长轮询连接：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

在 JavaScript 客户端中，通过设置提供给 的选项`transport`对象上的字段来`withUrl`配置传输：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>配置无记名身份验证

要提供身份验证数据以及 SignalR 请求，请使用`AccessTokenProvider`选项`accessTokenFactory`（在 JavaScript 中）指定返回所需访问令牌的函数。 在 .NET 客户端中，此访问令牌作为 HTTP"承载身份验证"令牌传入（使用`Authorization`具有 的`Bearer`标头的类型）。 在 JavaScript 客户端中，访问令牌用作承载令牌，**除非**浏览器 API 限制应用标头的能力（特别是在服务器发送事件和 WebSocket 请求中）。 在这些情况下，访问令牌作为查询字符串值`access_token`提供。

在 .NET 客户端中`AccessTokenProvider`，可以使用 中`WithUrl`的选项委托指定该选项：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

在 JavaScript 客户端中，通过设置 中`accessTokenFactory``withUrl`的选项对象上的字段来配置访问令牌。

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

在 SignalR Java 客户端中，可以通过向[HttpHubConnectBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)提供访问令牌工厂来配置承载令牌以用于身份验证。 [与AccessTokenFactory一起](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava)[单\<字符串>。 ](https://reactivex.io/documentation/single.html) 调用[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来生成客户端的访问令牌。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>配置超时和保持活动选项

`HubConnection`用于配置超时和保持活动行为的其他选项在对象本身上可用：

# <a name="net"></a>[.NET](#tab/dotnet)

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 秒（30，000 毫秒） | 服务器活动的超时。 如果服务器未在此间隔内发送消息，则客户端将认为服务器已断开连接并触发`Closed`事件（`onclose`在 JavaScript 中）。 此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。 建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。 |
| `HandshakeTimeout` | 15 秒 | 初始服务器握手的超时。 如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`Closed`事件（`onclose`在 JavaScript 中）。 这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。 有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |

在 .NET 客户端中，超时值指定为`TimeSpan`值。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 秒（30，000 毫秒） | 服务器活动的超时。 如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onclose`事件。 此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。 建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。 |

# <a name="java"></a>[Java](#tab/java)

| 选项 | 默认值 | 说明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 秒（30，000 毫秒） | 服务器活动的超时。 如果服务器未在此间隔内发送消息，则客户端认为服务器已断开连接并触发`onClose`事件。 此值必须足够大，以便**从服务器发送**ping 消息并由客户端在超时间隔内接收。 建议的值是一个数字，至少比服务器`KeepAliveInterval`的值翻倍，以便 ping 有时间到达。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初始服务器握手的超时。 如果服务器未在此间隔内发送握手响应，客户端将取消握手并触发`onClose`事件。 这是一个高级设置，只有在由于严重的网络延迟而发生握手超时错误时，才应修改该设置。 有关握手过程的更多详细信息，请参阅[SignalR 集线器协议规范](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)。 |

---

### <a name="configure-additional-options"></a>配置其他选项

其他选项可以在 Java 客户端`WithUrl`上`withUrl`或 Java 客户端上`HubConnectionBuilder`的各种配置 API 上（JavaScript）方法`HttpHubConnectionBuilder`中配置：

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET 选项 |  默认值 | 说明 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | 返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。 |
| `SkipNegotiation` | `false` | 将此`true`设置为跳过协商步骤。 **仅当 WebSocket 传输是唯一启用的传输时才受支持**。 使用 Azure SignalR 服务时无法启用此设置。 |
| `ClientCertificates` | 空 | 要发送到身份验证请求的 TLS 证书的集合。 |
| `Cookies` | 空 | 要随每个 HTTP 请求一起发送的 HTTP Cookie 集合。 |
| `Credentials` | 空 | 要随每个 HTTP 请求一起发送的凭据。 |
| `CloseTimeout` | 5 秒 | 仅限 Web 插座。 客户端关闭后等待服务器确认关闭请求的最大时间量。 如果服务器在此时间内未确认关闭，则客户端将断开连接。 |
| `Headers` | 空 | 要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。 |
| `HttpMessageHandlerFactory` | `null` | 可用于配置或替换`HttpMessageHandler`用于发送 HTTP 请求的委托。 不用于 Web 套接字连接。 此委托必须返回一个非空值，并且它将作为参数接收默认值。 修改该默认值上的设置并返回该设置，或返回新`HttpMessageHandler`实例。 **替换处理程序时，请确保从提供的处理程序复制要保留的设置，否则，配置的选项（如 Cookie 和标头）将不适用于新处理程序。** |
| `Proxy` | `null` | 发送 HTTP 请求时要使用的 HTTP 代理。 |
| `UseDefaultCredentials` | `false` | 设置此布尔以发送 HTTP 和 WebSocket 请求的默认凭据。 这支持使用 Windows 身份验证。 |
| `WebSocketConfiguration` | `null` | 可用于配置其他 WebSocket 选项的委托。 接收可用于配置选项的[客户端WebSocket选项](/dotnet/api/system.net.websockets.clientwebsocketoptions)的实例。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript 选项 | 默认值 | 说明 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | 返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。 |
| `skipNegotiation` | `false` | 将此`true`设置为跳过协商步骤。 **仅当 WebSocket 传输是唯一启用的传输时才受支持**。 使用 Azure SignalR 服务时无法启用此设置。 |

# <a name="java"></a>[Java](#tab/java)

| Java 选项 | 默认值 | 说明 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | 返回在 HTTP 请求中作为承载身份验证令牌提供的字符串的函数。 |
| `shouldSkipNegotiate` | `false` | 将此`true`设置为跳过协商步骤。 **仅当 WebSocket 传输是唯一启用的传输时才受支持**。 使用 Azure SignalR 服务时无法启用此设置。 |
| `withHeader` `withHeaders` | 空 | 要随每个 HTTP 请求一起发送的其他 HTTP 标头的映射。 |

---

在 .NET 客户端中，这些选项可以通过提供给`WithUrl`： 提供的选项委托进行修改：

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

在 JavaScript 客户端中，这些选项可以在提供给`withUrl`的 JavaScript 对象中提供：

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

在 Java 客户端中，可以使用`HttpHubConnectionBuilder`从`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>其他资源

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
