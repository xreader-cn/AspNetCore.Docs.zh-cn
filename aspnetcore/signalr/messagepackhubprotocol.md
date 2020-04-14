---
title: 在 ASP.NET 核心中使用SignalR消息包集线器协议
author: bradygaster
description: 将消息包集线器协议添加到SignalRASP.NET核心 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277231"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>在 ASP.NET 核心中使用SignalR消息包集线器协议

::: moniker range=">= aspnetcore-5.0"

本文假定读者[熟悉入门中](xref:tutorials/signalr)介绍的主题。

## <a name="what-is-messagepack"></a>什么是消息包？

[MessagePack](https://msgpack.org/index.html)是一种快速紧凑的二进制序列化格式。 当性能和带宽是一个问题时，它很有用，因为它创建的消息比[JSON](https://www.json.org/)少。 二进制消息在查看网络跟踪和日志时不可读，除非字节通过 MessagePack 解析器传递。 SignalR具有对 MessagePack 格式的内置支持，并为客户端和服务器提供了要使用的 API。

## <a name="configure-messagepack-on-the-server"></a>在服务器上配置消息包

要在服务器上启用 MessagePack 中心协议，请在`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`应用中安装包。 在`Startup.ConfigureServices`方法中，`AddMessagePackProtocol`添加到`AddSignalR`调用以在服务器上启用 MessagePack 支持。

> [!NOTE]
> 默认情况下启用 JSON。 添加消息包支持 JSON 和 MessagePack 客户端。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

要自定义 MessagePack 如何格式化数据，`AddMessagePackProtocol`请获取用于配置选项的委托。 在该委托中，`SerializerOptions`该属性可用于配置 MessagePack 序列化选项。 有关解析器如何工作的详细信息，请访问[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)中的 MessagePack 库。 属性可用于要序列化的对象，以定义如何处理它们。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> 我们强烈建议查看[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)并应用建议的修补程序。 例如，替换`.WithSecurity(MessagePackSecurity.UntrustedData)`时`SerializerOptions`调用 。

## <a name="configure-messagepack-on-the-client"></a>在客户端上配置消息包

> [!NOTE]
> 默认情况下，为支持的客户端启用 JSON。 客户端只能支持单个协议。 添加 MessagePack 支持将替换任何以前配置的协议。

### <a name="net-client"></a>.NET 客户端

要在 .NET 客户端中启用 MessagePack，`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`请安装`AddMessagePackProtocol`包`HubConnectionBuilder`并调用 。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 此`AddMessagePackProtocol`调用需要一个委托来配置类似于服务器的选项。

### <a name="javascript-client"></a>JavaScript 客户端

JavaScript 客户端的消息包支持由[@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack)npm 包提供。 通过在命令外壳中执行以下命令来安装包：

```bash
npm install @microsoft/signalr-protocol-msgpack
```

安装 npm 包后，模块可以直接通过 JavaScript 模块加载程序使用，或者通过引用以下文件导入浏览器：

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

在浏览器中，`msgpack5`还必须引用库。 使用标记`<script>`创建引用。 库可以在*node_modules_msgpack5\dist_msgpack5.js*中找到。

> [!NOTE]
> 使用 元素`<script>`时，顺序很重要。 如果在*msgpack5.js*之前引用*信号器协议-msgpack.js，* 则尝试与 MessagePack 连接时会发生错误。 *信号器.js*在*信号器协议-msgpack.js*之前也需要信号。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

将`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())``HubConnectionBuilder`客户端配置为在连接到服务器时使用 MessagePack 协议。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 此时，JavaScript 客户端上的 MessagePack 协议没有配置选项。

## <a name="messagepack-quirks"></a>消息包怪癖

使用 MessagePack 中心协议时，需要注意一些问题。

### <a name="messagepack-is-case-sensitive"></a>消息包区分大小写

MessagePack 协议区分大小写。 例如，请考虑以下 C# 类：

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

从 JavaScript 客户端发送时，必须使用`PascalCased`属性名称，因为大小写必须与 C# 类完全匹配。 例如：

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

使用`camelCased`名称不会正确绑定到 C# 类。 可以使用 属性`Key`为 MessagePack 属性指定其他名称来解决此问题。 有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>序列化/反序列化时不保留日期时间.Kind

MessagePack 协议不提供对`Kind`的值进行编码的方法。 `DateTime` 因此，在取消序列化日期时，MessagePack 中心协议将转换为 UTC 格式，`DateTime.Kind``DateTimeKind.Local`否则不会触摸时间并按现在的方式传递它。 如果您正在使用`DateTime`值，我们建议您在发送值之前转换为 UTC。 当您收到它们时，将它们从 UTC 转换为本地时间。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>JavaScript 中的消息包不支持日期时间.MinValue

JavaScript 客户端使用的[msgpack5](https://github.com/mcollina/msgpack5)库不支持 MessagePack`timestamp96`中的类型。 SignalR 此类型用于编码非常大的日期值（过去很早就或将来非常远）。 的值`DateTime.MinValue`必须`January 1, 0001`编码在`timestamp96`值中。 因此，不支持发送到`DateTime.MinValue`JavaScript 客户端。 当`DateTime.MinValue`JavaScript 客户端收到时，将引发以下错误：

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常，`DateTime.MinValue`用于编码"缺失"或`null`值。 如果需要在 MessagePack 中对该值进行编码，请使用空`DateTime`值 （`DateTime?`） 或编码`bool`单独的值，指示日期是否存在。

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228)。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"提前"编译环境中的消息包支持

.NET 客户端和服务器使用的[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90)库使用代码生成来优化序列化。 因此，默认情况下不支持使用"提前"编译（如 Xamarin iOS 或 Unity）的环境。 可以通过"预生成"序列化器/反序列化器代码在这些环境中使用 MessagePack。 有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)。 预先生成序列化器后，可以使用传递给 的配置委托注册它们`AddMessagePackProtocol`。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>消息包中的类型检查更加严格

JSON 中心协议将在反序列化期间执行类型转换。 例如，如果传入对象的属性值为数字 （），`{ foo: 42 }`但 .NET 类上的属性为类型`string`，则将转换该值。 但是，MessagePack 不执行此转换，并且将引发在服务器端日志（和控制台中）中可以看到的异常：

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937)。

## <a name="related-resources"></a>相关资源

* [入门](xref:tutorials/signalr)
* [.NET 客户端](xref:signalr/dotnet-client)
* [JavaScript 客户端](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

本文假定读者[熟悉入门中](xref:tutorials/signalr)介绍的主题。

## <a name="what-is-messagepack"></a>什么是消息包？

[MessagePack](https://msgpack.org/index.html)是一种快速紧凑的二进制序列化格式。 当性能和带宽是一个问题时，它很有用，因为它创建的消息比[JSON](https://www.json.org/)少。 二进制消息在查看网络跟踪和日志时不可读，除非字节通过 MessagePack 解析器传递。 SignalR具有对 MessagePack 格式的内置支持，并为客户端和服务器提供了要使用的 API。

## <a name="configure-messagepack-on-the-server"></a>在服务器上配置消息包

要在服务器上启用 MessagePack 中心协议，请在`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`应用中安装包。 在`Startup.ConfigureServices`方法中，`AddMessagePackProtocol`添加到`AddSignalR`调用以在服务器上启用 MessagePack 支持。

> [!NOTE]
> 默认情况下启用 JSON。 添加消息包支持 JSON 和 MessagePack 客户端。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

要自定义 MessagePack 如何格式化数据，`AddMessagePackProtocol`请获取用于配置选项的委托。 在该委托中，`FormatterResolvers`该属性可用于配置 MessagePack 序列化选项。 有关解析器如何工作的详细信息，请访问[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)中的 MessagePack 库。 属性可用于要序列化的对象，以定义如何处理它们。

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

> [!WARNING]
> 我们强烈建议查看[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)并应用建议的修补程序。 例如，将`MessagePackSecurity.Active`静态属性设置为`MessagePackSecurity.UntrustedData`。 设置`MessagePackSecurity.Active`需要手动安装[1.9.x 版本的 MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3)。 安装`MessagePack`1.9.x 升级SignalR版本使用。 当`MessagePackSecurity.Active`未设置为`MessagePackSecurity.UntrustedData`时，恶意客户端可能会导致拒绝服务。 在`MessagePackSecurity.Active``Program.Main`中设置，如以下代码所示：

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>在客户端上配置消息包

> [!NOTE]
> 默认情况下，为支持的客户端启用 JSON。 客户端只能支持单个协议。 添加 MessagePack 支持将替换任何以前配置的协议。

### <a name="net-client"></a>.NET 客户端

要在 .NET 客户端中启用 MessagePack，`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`请安装`AddMessagePackProtocol`包`HubConnectionBuilder`并调用 。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 此`AddMessagePackProtocol`调用需要一个委托来配置类似于服务器的选项。

### <a name="javascript-client"></a>JavaScript 客户端

JavaScript 客户端的消息包支持由[@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack)npm 包提供。 通过在命令外壳中执行以下命令来安装包：

```bash
npm install @microsoft/signalr-protocol-msgpack
```

安装 npm 包后，模块可以直接通过 JavaScript 模块加载程序使用，或者通过引用以下文件导入浏览器：

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

在浏览器中，`msgpack5`还必须引用库。 使用标记`<script>`创建引用。 库可以在*node_modules_msgpack5\dist_msgpack5.js*中找到。

> [!NOTE]
> 使用 元素`<script>`时，顺序很重要。 如果在*msgpack5.js*之前引用*信号器协议-msgpack.js，* 则尝试与 MessagePack 连接时会发生错误。 *信号器.js*在*信号器协议-msgpack.js*之前也需要信号。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

将`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())``HubConnectionBuilder`客户端配置为在连接到服务器时使用 MessagePack 协议。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 此时，JavaScript 客户端上的 MessagePack 协议没有配置选项。

## <a name="messagepack-quirks"></a>消息包怪癖

使用 MessagePack 中心协议时，需要注意一些问题。

### <a name="messagepack-is-case-sensitive"></a>消息包区分大小写

MessagePack 协议区分大小写。 例如，请考虑以下 C# 类：

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

从 JavaScript 客户端发送时，必须使用`PascalCased`属性名称，因为大小写必须与 C# 类完全匹配。 例如：

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

使用`camelCased`名称不会正确绑定到 C# 类。 可以使用 属性`Key`为 MessagePack 属性指定其他名称来解决此问题。 有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>序列化/反序列化时不保留日期时间.Kind

MessagePack 协议不提供对`Kind`的值进行编码的方法。 `DateTime` 因此，当对日期进行反序列化时，MessagePack 中心协议假定传入日期为 UTC 格式。 如果您在本地时间使用`DateTime`值，我们建议您在发送之前转换为 UTC。 当您收到它们时，将它们从 UTC 转换为本地时间。

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632)。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>JavaScript 中的消息包不支持日期时间.MinValue

JavaScript 客户端使用的[msgpack5](https://github.com/mcollina/msgpack5)库不支持 MessagePack`timestamp96`中的类型。 SignalR 此类型用于编码非常大的日期值（过去很早就或将来非常远）。 的值`DateTime.MinValue`必须`January 1, 0001`编码在`timestamp96`值中。 因此，不支持发送到`DateTime.MinValue`JavaScript 客户端。 当`DateTime.MinValue`JavaScript 客户端收到时，将引发以下错误：

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常，`DateTime.MinValue`用于编码"缺失"或`null`值。 如果需要在 MessagePack 中对该值进行编码，请使用空`DateTime`值 （`DateTime?`） 或编码`bool`单独的值，指示日期是否存在。

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228)。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"提前"编译环境中的消息包支持

.NET 客户端和服务器使用的[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)库使用代码生成来优化序列化。 因此，默认情况下不支持使用"提前"编译（如 Xamarin iOS 或 Unity）的环境。 可以通过"预生成"序列化器/反序列化器代码在这些环境中使用 MessagePack。 有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。 预先生成序列化器后，可以使用传递给 的配置委托注册它们`AddMessagePackProtocol`。

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>消息包中的类型检查更加严格

JSON 中心协议将在反序列化期间执行类型转换。 例如，如果传入对象的属性值为数字 （），`{ foo: 42 }`但 .NET 类上的属性为类型`string`，则将转换该值。 但是，MessagePack 不执行此转换，并且将引发在服务器端日志（和控制台中）中可以看到的异常：

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937)。

## <a name="related-resources"></a>相关资源

* [入门](xref:tutorials/signalr)
* [.NET 客户端](xref:signalr/dotnet-client)
* [JavaScript 客户端](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本文假定读者[熟悉入门中](xref:tutorials/signalr)介绍的主题。

## <a name="what-is-messagepack"></a>什么是消息包？

[MessagePack](https://msgpack.org/index.html)是一种快速紧凑的二进制序列化格式。 当性能和带宽是一个问题时，它很有用，因为它创建的消息比[JSON](https://www.json.org/)少。 二进制消息在查看网络跟踪和日志时不可读，除非字节通过 MessagePack 解析器传递。 SignalR具有对 MessagePack 格式的内置支持，并为客户端和服务器提供了要使用的 API。

## <a name="configure-messagepack-on-the-server"></a>在服务器上配置消息包

要在服务器上启用 MessagePack 中心协议，请在`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`应用中安装包。 在`Startup.ConfigureServices`方法中，`AddMessagePackProtocol`添加到`AddSignalR`调用以在服务器上启用 MessagePack 支持。

> [!NOTE]
> 默认情况下启用 JSON。 添加消息包支持 JSON 和 MessagePack 客户端。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

要自定义 MessagePack 如何格式化数据，`AddMessagePackProtocol`请获取用于配置选项的委托。 在该委托中，`FormatterResolvers`该属性可用于配置 MessagePack 序列化选项。 有关解析器如何工作的详细信息，请访问[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)中的 MessagePack 库。 属性可用于要序列化的对象，以定义如何处理它们。

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

> [!WARNING]
> 我们强烈建议查看[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)并应用建议的修补程序。 例如，将`MessagePackSecurity.Active`静态属性设置为`MessagePackSecurity.UntrustedData`。 设置`MessagePackSecurity.Active`需要手动安装[1.9.x 版本的 MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3)。 安装`MessagePack`1.9.x 升级SignalR版本使用。 当`MessagePackSecurity.Active`未设置为`MessagePackSecurity.UntrustedData`时，恶意客户端可能会导致拒绝服务。 在`MessagePackSecurity.Active``Program.Main`中设置，如以下代码所示：

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>在客户端上配置消息包

> [!NOTE]
> 默认情况下，为支持的客户端启用 JSON。 客户端只能支持单个协议。 添加 MessagePack 支持将替换任何以前配置的协议。

### <a name="net-client"></a>.NET 客户端

要在 .NET 客户端中启用 MessagePack，`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`请安装`AddMessagePackProtocol`包`HubConnectionBuilder`并调用 。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> 此`AddMessagePackProtocol`调用需要一个委托来配置类似于服务器的选项。

### <a name="javascript-client"></a>JavaScript 客户端

JavaScript 客户端的消息包支持由[@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack)npm 包提供。 通过在命令外壳中执行以下命令来安装包：

```bash
npm install @aspnet/signalr-protocol-msgpack
```

安装 npm 包后，模块可以直接通过 JavaScript 模块加载程序使用，或者通过引用以下文件导入浏览器：

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

在浏览器中，`msgpack5`还必须引用库。 使用标记`<script>`创建引用。 库可以在*node_modules_msgpack5\dist_msgpack5.js*中找到。

> [!NOTE]
> 使用 元素`<script>`时，顺序很重要。 如果在*msgpack5.js*之前引用*信号器协议-msgpack.js，* 则尝试与 MessagePack 连接时会发生错误。 *信号器.js*在*信号器协议-msgpack.js*之前也需要信号。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

将`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())``HubConnectionBuilder`客户端配置为在连接到服务器时使用 MessagePack 协议。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 此时，JavaScript 客户端上的 MessagePack 协议没有配置选项。

## <a name="messagepack-quirks"></a>消息包怪癖

使用 MessagePack 中心协议时，需要注意一些问题。

### <a name="messagepack-is-case-sensitive"></a>消息包区分大小写

MessagePack 协议区分大小写。 例如，请考虑以下 C# 类：

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

从 JavaScript 客户端发送时，必须使用`PascalCased`属性名称，因为大小写必须与 C# 类完全匹配。 例如：

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

使用`camelCased`名称不会正确绑定到 C# 类。 可以使用 属性`Key`为 MessagePack 属性指定其他名称来解决此问题。 有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>序列化/反序列化时不保留日期时间.Kind

MessagePack 协议不提供对`Kind`的值进行编码的方法。 `DateTime` 因此，当对日期进行反序列化时，MessagePack 中心协议假定传入日期为 UTC 格式。 如果您在本地时间使用`DateTime`值，我们建议您在发送之前转换为 UTC。 当您收到它们时，将它们从 UTC 转换为本地时间。

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632)。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>JavaScript 中的消息包不支持日期时间.MinValue

JavaScript 客户端使用的[msgpack5](https://github.com/mcollina/msgpack5)库不支持 MessagePack`timestamp96`中的类型。 SignalR 此类型用于编码非常大的日期值（过去很早就或将来非常远）。 的值`DateTime.MinValue``January 1, 0001`必须编码在`timestamp96`值中。 因此，不支持发送到`DateTime.MinValue`JavaScript 客户端。 当`DateTime.MinValue`JavaScript 客户端收到时，将引发以下错误：

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常，`DateTime.MinValue`用于编码"缺失"或`null`值。 如果需要在 MessagePack 中对该值进行编码，请使用空`DateTime`值 （`DateTime?`） 或编码`bool`单独的值，指示日期是否存在。

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228)。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"提前"编译环境中的消息包支持

.NET 客户端和服务器使用的[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)库使用代码生成来优化序列化。 因此，默认情况下不支持使用"提前"编译（如 Xamarin iOS 或 Unity）的环境。 可以通过"预生成"序列化器/反序列化器代码在这些环境中使用 MessagePack。 有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。 预先生成序列化器后，可以使用传递给 的配置委托注册它们`AddMessagePackProtocol`。

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>消息包中的类型检查更加严格

JSON 中心协议将在反序列化期间执行类型转换。 例如，如果传入对象的属性值为数字 （），`{ foo: 42 }`但 .NET 类上的属性为类型`string`，则将转换该值。 但是，MessagePack 不执行此转换，并且将引发在服务器端日志（和控制台中）中可以看到的异常：

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937)。

## <a name="related-resources"></a>相关资源

* [入门](xref:tutorials/signalr)
* [.NET 客户端](xref:signalr/dotnet-client)
* [JavaScript 客户端](xref:signalr/javascript-client)

::: moniker-end
