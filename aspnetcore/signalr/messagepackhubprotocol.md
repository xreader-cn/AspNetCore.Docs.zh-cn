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
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="92189-103">在 ASP.NET 核心中使用SignalR消息包集线器协议</span><span class="sxs-lookup"><span data-stu-id="92189-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="92189-104">本文假定读者[熟悉入门中](xref:tutorials/signalr)介绍的主题。</span><span class="sxs-lookup"><span data-stu-id="92189-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="92189-105">什么是消息包？</span><span class="sxs-lookup"><span data-stu-id="92189-105">What is MessagePack?</span></span>

<span data-ttu-id="92189-106">[MessagePack](https://msgpack.org/index.html)是一种快速紧凑的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="92189-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="92189-107">当性能和带宽是一个问题时，它很有用，因为它创建的消息比[JSON](https://www.json.org/)少。</span><span class="sxs-lookup"><span data-stu-id="92189-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="92189-108">二进制消息在查看网络跟踪和日志时不可读，除非字节通过 MessagePack 解析器传递。</span><span class="sxs-lookup"><span data-stu-id="92189-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="92189-109">具有对 MessagePack 格式的内置支持，并为客户端和服务器提供了要使用的 API。</span><span class="sxs-lookup"><span data-stu-id="92189-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="92189-110">在服务器上配置消息包</span><span class="sxs-lookup"><span data-stu-id="92189-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="92189-111">要在服务器上启用 MessagePack 中心协议，请在`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`应用中安装包。</span><span class="sxs-lookup"><span data-stu-id="92189-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="92189-112">在`Startup.ConfigureServices`方法中，`AddMessagePackProtocol`添加到`AddSignalR`调用以在服务器上启用 MessagePack 支持。</span><span class="sxs-lookup"><span data-stu-id="92189-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="92189-113">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="92189-113">JSON is enabled by default.</span></span> <span data-ttu-id="92189-114">添加消息包支持 JSON 和 MessagePack 客户端。</span><span class="sxs-lookup"><span data-stu-id="92189-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="92189-115">要自定义 MessagePack 如何格式化数据，`AddMessagePackProtocol`请获取用于配置选项的委托。</span><span class="sxs-lookup"><span data-stu-id="92189-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="92189-116">在该委托中，`SerializerOptions`该属性可用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="92189-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="92189-117">有关解析器如何工作的详细信息，请访问[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)中的 MessagePack 库。</span><span class="sxs-lookup"><span data-stu-id="92189-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="92189-118">属性可用于要序列化的对象，以定义如何处理它们。</span><span class="sxs-lookup"><span data-stu-id="92189-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="92189-119">我们强烈建议查看[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)并应用建议的修补程序。</span><span class="sxs-lookup"><span data-stu-id="92189-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="92189-120">例如，替换`.WithSecurity(MessagePackSecurity.UntrustedData)`时`SerializerOptions`调用 。</span><span class="sxs-lookup"><span data-stu-id="92189-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="92189-121">在客户端上配置消息包</span><span class="sxs-lookup"><span data-stu-id="92189-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="92189-122">默认情况下，为支持的客户端启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="92189-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="92189-123">客户端只能支持单个协议。</span><span class="sxs-lookup"><span data-stu-id="92189-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="92189-124">添加 MessagePack 支持将替换任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="92189-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="92189-125">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-125">.NET client</span></span>

<span data-ttu-id="92189-126">要在 .NET 客户端中启用 MessagePack，`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`请安装`AddMessagePackProtocol`包`HubConnectionBuilder`并调用 。</span><span class="sxs-lookup"><span data-stu-id="92189-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="92189-127">此`AddMessagePackProtocol`调用需要一个委托来配置类似于服务器的选项。</span><span class="sxs-lookup"><span data-stu-id="92189-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="92189-128">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-128">JavaScript client</span></span>

<span data-ttu-id="92189-129">JavaScript 客户端的消息包支持由[@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack)npm 包提供。</span><span class="sxs-lookup"><span data-stu-id="92189-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="92189-130">通过在命令外壳中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="92189-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="92189-131">安装 npm 包后，模块可以直接通过 JavaScript 模块加载程序使用，或者通过引用以下文件导入浏览器：</span><span class="sxs-lookup"><span data-stu-id="92189-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="92189-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="92189-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="92189-133">在浏览器中，`msgpack5`还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="92189-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="92189-134">使用标记`<script>`创建引用。</span><span class="sxs-lookup"><span data-stu-id="92189-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="92189-135">库可以在*node_modules_msgpack5\dist_msgpack5.js*中找到。</span><span class="sxs-lookup"><span data-stu-id="92189-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="92189-136">使用 元素`<script>`时，顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="92189-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="92189-137">如果在*msgpack5.js*之前引用*信号器协议-msgpack.js，* 则尝试与 MessagePack 连接时会发生错误。</span><span class="sxs-lookup"><span data-stu-id="92189-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="92189-138">*信号器.js*在*信号器协议-msgpack.js*之前也需要信号。</span><span class="sxs-lookup"><span data-stu-id="92189-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="92189-139">将`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())``HubConnectionBuilder`客户端配置为在连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="92189-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="92189-140">此时，JavaScript 客户端上的 MessagePack 协议没有配置选项。</span><span class="sxs-lookup"><span data-stu-id="92189-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="92189-141">消息包怪癖</span><span class="sxs-lookup"><span data-stu-id="92189-141">MessagePack quirks</span></span>

<span data-ttu-id="92189-142">使用 MessagePack 中心协议时，需要注意一些问题。</span><span class="sxs-lookup"><span data-stu-id="92189-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="92189-143">消息包区分大小写</span><span class="sxs-lookup"><span data-stu-id="92189-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="92189-144">MessagePack 协议区分大小写。</span><span class="sxs-lookup"><span data-stu-id="92189-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="92189-145">例如，请考虑以下 C# 类：</span><span class="sxs-lookup"><span data-stu-id="92189-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="92189-146">从 JavaScript 客户端发送时，必须使用`PascalCased`属性名称，因为大小写必须与 C# 类完全匹配。</span><span class="sxs-lookup"><span data-stu-id="92189-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="92189-147">例如：</span><span class="sxs-lookup"><span data-stu-id="92189-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="92189-148">使用`camelCased`名称不会正确绑定到 C# 类。</span><span class="sxs-lookup"><span data-stu-id="92189-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="92189-149">可以使用 属性`Key`为 MessagePack 属性指定其他名称来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="92189-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="92189-150">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="92189-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="92189-151">序列化/反序列化时不保留日期时间.Kind</span><span class="sxs-lookup"><span data-stu-id="92189-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="92189-152">MessagePack 协议不提供对`Kind`的值进行编码的方法。 `DateTime`</span><span class="sxs-lookup"><span data-stu-id="92189-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="92189-153">因此，在取消序列化日期时，MessagePack 中心协议将转换为 UTC 格式，`DateTime.Kind``DateTimeKind.Local`否则不会触摸时间并按现在的方式传递它。</span><span class="sxs-lookup"><span data-stu-id="92189-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="92189-154">如果您正在使用`DateTime`值，我们建议您在发送值之前转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="92189-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="92189-155">当您收到它们时，将它们从 UTC 转换为本地时间。</span><span class="sxs-lookup"><span data-stu-id="92189-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="92189-156">JavaScript 中的消息包不支持日期时间.MinValue</span><span class="sxs-lookup"><span data-stu-id="92189-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="92189-157">JavaScript 客户端使用的[msgpack5](https://github.com/mcollina/msgpack5)库不支持 MessagePack`timestamp96`中的类型。 SignalR</span><span class="sxs-lookup"><span data-stu-id="92189-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="92189-158">此类型用于编码非常大的日期值（过去很早就或将来非常远）。</span><span class="sxs-lookup"><span data-stu-id="92189-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="92189-159">的值`DateTime.MinValue`必须`January 1, 0001`编码在`timestamp96`值中。</span><span class="sxs-lookup"><span data-stu-id="92189-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="92189-160">因此，不支持发送到`DateTime.MinValue`JavaScript 客户端。</span><span class="sxs-lookup"><span data-stu-id="92189-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="92189-161">当`DateTime.MinValue`JavaScript 客户端收到时，将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="92189-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="92189-162">通常，`DateTime.MinValue`用于编码"缺失"或`null`值。</span><span class="sxs-lookup"><span data-stu-id="92189-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="92189-163">如果需要在 MessagePack 中对该值进行编码，请使用空`DateTime`值 （`DateTime?`） 或编码`bool`单独的值，指示日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="92189-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="92189-164">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="92189-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="92189-165">"提前"编译环境中的消息包支持</span><span class="sxs-lookup"><span data-stu-id="92189-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="92189-166">.NET 客户端和服务器使用的[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90)库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="92189-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="92189-167">因此，默认情况下不支持使用"提前"编译（如 Xamarin iOS 或 Unity）的环境。</span><span class="sxs-lookup"><span data-stu-id="92189-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="92189-168">可以通过"预生成"序列化器/反序列化器代码在这些环境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="92189-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="92189-169">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)。</span><span class="sxs-lookup"><span data-stu-id="92189-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="92189-170">预先生成序列化器后，可以使用传递给 的配置委托注册它们`AddMessagePackProtocol`。</span><span class="sxs-lookup"><span data-stu-id="92189-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="92189-171">消息包中的类型检查更加严格</span><span class="sxs-lookup"><span data-stu-id="92189-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="92189-172">JSON 中心协议将在反序列化期间执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="92189-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="92189-173">例如，如果传入对象的属性值为数字 （），`{ foo: 42 }`但 .NET 类上的属性为类型`string`，则将转换该值。</span><span class="sxs-lookup"><span data-stu-id="92189-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="92189-174">但是，MessagePack 不执行此转换，并且将引发在服务器端日志（和控制台中）中可以看到的异常：</span><span class="sxs-lookup"><span data-stu-id="92189-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="92189-175">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="92189-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="92189-176">相关资源</span><span class="sxs-lookup"><span data-stu-id="92189-176">Related resources</span></span>

* [<span data-ttu-id="92189-177">入门</span><span class="sxs-lookup"><span data-stu-id="92189-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="92189-178">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="92189-179">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="92189-180">本文假定读者[熟悉入门中](xref:tutorials/signalr)介绍的主题。</span><span class="sxs-lookup"><span data-stu-id="92189-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="92189-181">什么是消息包？</span><span class="sxs-lookup"><span data-stu-id="92189-181">What is MessagePack?</span></span>

<span data-ttu-id="92189-182">[MessagePack](https://msgpack.org/index.html)是一种快速紧凑的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="92189-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="92189-183">当性能和带宽是一个问题时，它很有用，因为它创建的消息比[JSON](https://www.json.org/)少。</span><span class="sxs-lookup"><span data-stu-id="92189-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="92189-184">二进制消息在查看网络跟踪和日志时不可读，除非字节通过 MessagePack 解析器传递。</span><span class="sxs-lookup"><span data-stu-id="92189-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="92189-185">具有对 MessagePack 格式的内置支持，并为客户端和服务器提供了要使用的 API。</span><span class="sxs-lookup"><span data-stu-id="92189-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="92189-186">在服务器上配置消息包</span><span class="sxs-lookup"><span data-stu-id="92189-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="92189-187">要在服务器上启用 MessagePack 中心协议，请在`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`应用中安装包。</span><span class="sxs-lookup"><span data-stu-id="92189-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="92189-188">在`Startup.ConfigureServices`方法中，`AddMessagePackProtocol`添加到`AddSignalR`调用以在服务器上启用 MessagePack 支持。</span><span class="sxs-lookup"><span data-stu-id="92189-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="92189-189">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="92189-189">JSON is enabled by default.</span></span> <span data-ttu-id="92189-190">添加消息包支持 JSON 和 MessagePack 客户端。</span><span class="sxs-lookup"><span data-stu-id="92189-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="92189-191">要自定义 MessagePack 如何格式化数据，`AddMessagePackProtocol`请获取用于配置选项的委托。</span><span class="sxs-lookup"><span data-stu-id="92189-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="92189-192">在该委托中，`FormatterResolvers`该属性可用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="92189-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="92189-193">有关解析器如何工作的详细信息，请访问[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)中的 MessagePack 库。</span><span class="sxs-lookup"><span data-stu-id="92189-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="92189-194">属性可用于要序列化的对象，以定义如何处理它们。</span><span class="sxs-lookup"><span data-stu-id="92189-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="92189-195">我们强烈建议查看[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)并应用建议的修补程序。</span><span class="sxs-lookup"><span data-stu-id="92189-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="92189-196">例如，将`MessagePackSecurity.Active`静态属性设置为`MessagePackSecurity.UntrustedData`。</span><span class="sxs-lookup"><span data-stu-id="92189-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="92189-197">设置`MessagePackSecurity.Active`需要手动安装[1.9.x 版本的 MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3)。</span><span class="sxs-lookup"><span data-stu-id="92189-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="92189-198">安装`MessagePack`1.9.x 升级SignalR版本使用。</span><span class="sxs-lookup"><span data-stu-id="92189-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="92189-199">当`MessagePackSecurity.Active`未设置为`MessagePackSecurity.UntrustedData`时，恶意客户端可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="92189-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="92189-200">在`MessagePackSecurity.Active``Program.Main`中设置，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="92189-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="92189-201">在客户端上配置消息包</span><span class="sxs-lookup"><span data-stu-id="92189-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="92189-202">默认情况下，为支持的客户端启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="92189-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="92189-203">客户端只能支持单个协议。</span><span class="sxs-lookup"><span data-stu-id="92189-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="92189-204">添加 MessagePack 支持将替换任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="92189-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="92189-205">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-205">.NET client</span></span>

<span data-ttu-id="92189-206">要在 .NET 客户端中启用 MessagePack，`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`请安装`AddMessagePackProtocol`包`HubConnectionBuilder`并调用 。</span><span class="sxs-lookup"><span data-stu-id="92189-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="92189-207">此`AddMessagePackProtocol`调用需要一个委托来配置类似于服务器的选项。</span><span class="sxs-lookup"><span data-stu-id="92189-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="92189-208">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-208">JavaScript client</span></span>

<span data-ttu-id="92189-209">JavaScript 客户端的消息包支持由[@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack)npm 包提供。</span><span class="sxs-lookup"><span data-stu-id="92189-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="92189-210">通过在命令外壳中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="92189-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="92189-211">安装 npm 包后，模块可以直接通过 JavaScript 模块加载程序使用，或者通过引用以下文件导入浏览器：</span><span class="sxs-lookup"><span data-stu-id="92189-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="92189-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="92189-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="92189-213">在浏览器中，`msgpack5`还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="92189-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="92189-214">使用标记`<script>`创建引用。</span><span class="sxs-lookup"><span data-stu-id="92189-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="92189-215">库可以在*node_modules_msgpack5\dist_msgpack5.js*中找到。</span><span class="sxs-lookup"><span data-stu-id="92189-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="92189-216">使用 元素`<script>`时，顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="92189-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="92189-217">如果在*msgpack5.js*之前引用*信号器协议-msgpack.js，* 则尝试与 MessagePack 连接时会发生错误。</span><span class="sxs-lookup"><span data-stu-id="92189-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="92189-218">*信号器.js*在*信号器协议-msgpack.js*之前也需要信号。</span><span class="sxs-lookup"><span data-stu-id="92189-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="92189-219">将`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())``HubConnectionBuilder`客户端配置为在连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="92189-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="92189-220">此时，JavaScript 客户端上的 MessagePack 协议没有配置选项。</span><span class="sxs-lookup"><span data-stu-id="92189-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="92189-221">消息包怪癖</span><span class="sxs-lookup"><span data-stu-id="92189-221">MessagePack quirks</span></span>

<span data-ttu-id="92189-222">使用 MessagePack 中心协议时，需要注意一些问题。</span><span class="sxs-lookup"><span data-stu-id="92189-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="92189-223">消息包区分大小写</span><span class="sxs-lookup"><span data-stu-id="92189-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="92189-224">MessagePack 协议区分大小写。</span><span class="sxs-lookup"><span data-stu-id="92189-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="92189-225">例如，请考虑以下 C# 类：</span><span class="sxs-lookup"><span data-stu-id="92189-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="92189-226">从 JavaScript 客户端发送时，必须使用`PascalCased`属性名称，因为大小写必须与 C# 类完全匹配。</span><span class="sxs-lookup"><span data-stu-id="92189-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="92189-227">例如：</span><span class="sxs-lookup"><span data-stu-id="92189-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="92189-228">使用`camelCased`名称不会正确绑定到 C# 类。</span><span class="sxs-lookup"><span data-stu-id="92189-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="92189-229">可以使用 属性`Key`为 MessagePack 属性指定其他名称来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="92189-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="92189-230">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="92189-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="92189-231">序列化/反序列化时不保留日期时间.Kind</span><span class="sxs-lookup"><span data-stu-id="92189-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="92189-232">MessagePack 协议不提供对`Kind`的值进行编码的方法。 `DateTime`</span><span class="sxs-lookup"><span data-stu-id="92189-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="92189-233">因此，当对日期进行反序列化时，MessagePack 中心协议假定传入日期为 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="92189-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="92189-234">如果您在本地时间使用`DateTime`值，我们建议您在发送之前转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="92189-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="92189-235">当您收到它们时，将它们从 UTC 转换为本地时间。</span><span class="sxs-lookup"><span data-stu-id="92189-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="92189-236">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="92189-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="92189-237">JavaScript 中的消息包不支持日期时间.MinValue</span><span class="sxs-lookup"><span data-stu-id="92189-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="92189-238">JavaScript 客户端使用的[msgpack5](https://github.com/mcollina/msgpack5)库不支持 MessagePack`timestamp96`中的类型。 SignalR</span><span class="sxs-lookup"><span data-stu-id="92189-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="92189-239">此类型用于编码非常大的日期值（过去很早就或将来非常远）。</span><span class="sxs-lookup"><span data-stu-id="92189-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="92189-240">的值`DateTime.MinValue`必须`January 1, 0001`编码在`timestamp96`值中。</span><span class="sxs-lookup"><span data-stu-id="92189-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="92189-241">因此，不支持发送到`DateTime.MinValue`JavaScript 客户端。</span><span class="sxs-lookup"><span data-stu-id="92189-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="92189-242">当`DateTime.MinValue`JavaScript 客户端收到时，将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="92189-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="92189-243">通常，`DateTime.MinValue`用于编码"缺失"或`null`值。</span><span class="sxs-lookup"><span data-stu-id="92189-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="92189-244">如果需要在 MessagePack 中对该值进行编码，请使用空`DateTime`值 （`DateTime?`） 或编码`bool`单独的值，指示日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="92189-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="92189-245">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="92189-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="92189-246">"提前"编译环境中的消息包支持</span><span class="sxs-lookup"><span data-stu-id="92189-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="92189-247">.NET 客户端和服务器使用的[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="92189-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="92189-248">因此，默认情况下不支持使用"提前"编译（如 Xamarin iOS 或 Unity）的环境。</span><span class="sxs-lookup"><span data-stu-id="92189-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="92189-249">可以通过"预生成"序列化器/反序列化器代码在这些环境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="92189-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="92189-250">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="92189-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="92189-251">预先生成序列化器后，可以使用传递给 的配置委托注册它们`AddMessagePackProtocol`。</span><span class="sxs-lookup"><span data-stu-id="92189-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="92189-252">消息包中的类型检查更加严格</span><span class="sxs-lookup"><span data-stu-id="92189-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="92189-253">JSON 中心协议将在反序列化期间执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="92189-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="92189-254">例如，如果传入对象的属性值为数字 （），`{ foo: 42 }`但 .NET 类上的属性为类型`string`，则将转换该值。</span><span class="sxs-lookup"><span data-stu-id="92189-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="92189-255">但是，MessagePack 不执行此转换，并且将引发在服务器端日志（和控制台中）中可以看到的异常：</span><span class="sxs-lookup"><span data-stu-id="92189-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="92189-256">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="92189-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="92189-257">相关资源</span><span class="sxs-lookup"><span data-stu-id="92189-257">Related resources</span></span>

* [<span data-ttu-id="92189-258">入门</span><span class="sxs-lookup"><span data-stu-id="92189-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="92189-259">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="92189-260">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="92189-261">本文假定读者[熟悉入门中](xref:tutorials/signalr)介绍的主题。</span><span class="sxs-lookup"><span data-stu-id="92189-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="92189-262">什么是消息包？</span><span class="sxs-lookup"><span data-stu-id="92189-262">What is MessagePack?</span></span>

<span data-ttu-id="92189-263">[MessagePack](https://msgpack.org/index.html)是一种快速紧凑的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="92189-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="92189-264">当性能和带宽是一个问题时，它很有用，因为它创建的消息比[JSON](https://www.json.org/)少。</span><span class="sxs-lookup"><span data-stu-id="92189-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="92189-265">二进制消息在查看网络跟踪和日志时不可读，除非字节通过 MessagePack 解析器传递。</span><span class="sxs-lookup"><span data-stu-id="92189-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="92189-266">具有对 MessagePack 格式的内置支持，并为客户端和服务器提供了要使用的 API。</span><span class="sxs-lookup"><span data-stu-id="92189-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="92189-267">在服务器上配置消息包</span><span class="sxs-lookup"><span data-stu-id="92189-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="92189-268">要在服务器上启用 MessagePack 中心协议，请在`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`应用中安装包。</span><span class="sxs-lookup"><span data-stu-id="92189-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="92189-269">在`Startup.ConfigureServices`方法中，`AddMessagePackProtocol`添加到`AddSignalR`调用以在服务器上启用 MessagePack 支持。</span><span class="sxs-lookup"><span data-stu-id="92189-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="92189-270">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="92189-270">JSON is enabled by default.</span></span> <span data-ttu-id="92189-271">添加消息包支持 JSON 和 MessagePack 客户端。</span><span class="sxs-lookup"><span data-stu-id="92189-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="92189-272">要自定义 MessagePack 如何格式化数据，`AddMessagePackProtocol`请获取用于配置选项的委托。</span><span class="sxs-lookup"><span data-stu-id="92189-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="92189-273">在该委托中，`FormatterResolvers`该属性可用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="92189-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="92189-274">有关解析器如何工作的详细信息，请访问[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)中的 MessagePack 库。</span><span class="sxs-lookup"><span data-stu-id="92189-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="92189-275">属性可用于要序列化的对象，以定义如何处理它们。</span><span class="sxs-lookup"><span data-stu-id="92189-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="92189-276">我们强烈建议查看[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)并应用建议的修补程序。</span><span class="sxs-lookup"><span data-stu-id="92189-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="92189-277">例如，将`MessagePackSecurity.Active`静态属性设置为`MessagePackSecurity.UntrustedData`。</span><span class="sxs-lookup"><span data-stu-id="92189-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="92189-278">设置`MessagePackSecurity.Active`需要手动安装[1.9.x 版本的 MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3)。</span><span class="sxs-lookup"><span data-stu-id="92189-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="92189-279">安装`MessagePack`1.9.x 升级SignalR版本使用。</span><span class="sxs-lookup"><span data-stu-id="92189-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="92189-280">当`MessagePackSecurity.Active`未设置为`MessagePackSecurity.UntrustedData`时，恶意客户端可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="92189-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="92189-281">在`MessagePackSecurity.Active``Program.Main`中设置，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="92189-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="92189-282">在客户端上配置消息包</span><span class="sxs-lookup"><span data-stu-id="92189-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="92189-283">默认情况下，为支持的客户端启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="92189-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="92189-284">客户端只能支持单个协议。</span><span class="sxs-lookup"><span data-stu-id="92189-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="92189-285">添加 MessagePack 支持将替换任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="92189-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="92189-286">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-286">.NET client</span></span>

<span data-ttu-id="92189-287">要在 .NET 客户端中启用 MessagePack，`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`请安装`AddMessagePackProtocol`包`HubConnectionBuilder`并调用 。</span><span class="sxs-lookup"><span data-stu-id="92189-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="92189-288">此`AddMessagePackProtocol`调用需要一个委托来配置类似于服务器的选项。</span><span class="sxs-lookup"><span data-stu-id="92189-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="92189-289">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-289">JavaScript client</span></span>

<span data-ttu-id="92189-290">JavaScript 客户端的消息包支持由[@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack)npm 包提供。</span><span class="sxs-lookup"><span data-stu-id="92189-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="92189-291">通过在命令外壳中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="92189-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="92189-292">安装 npm 包后，模块可以直接通过 JavaScript 模块加载程序使用，或者通过引用以下文件导入浏览器：</span><span class="sxs-lookup"><span data-stu-id="92189-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="92189-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="92189-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="92189-294">在浏览器中，`msgpack5`还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="92189-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="92189-295">使用标记`<script>`创建引用。</span><span class="sxs-lookup"><span data-stu-id="92189-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="92189-296">库可以在*node_modules_msgpack5\dist_msgpack5.js*中找到。</span><span class="sxs-lookup"><span data-stu-id="92189-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="92189-297">使用 元素`<script>`时，顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="92189-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="92189-298">如果在*msgpack5.js*之前引用*信号器协议-msgpack.js，* 则尝试与 MessagePack 连接时会发生错误。</span><span class="sxs-lookup"><span data-stu-id="92189-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="92189-299">*信号器.js*在*信号器协议-msgpack.js*之前也需要信号。</span><span class="sxs-lookup"><span data-stu-id="92189-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="92189-300">将`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())``HubConnectionBuilder`客户端配置为在连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="92189-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="92189-301">此时，JavaScript 客户端上的 MessagePack 协议没有配置选项。</span><span class="sxs-lookup"><span data-stu-id="92189-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="92189-302">消息包怪癖</span><span class="sxs-lookup"><span data-stu-id="92189-302">MessagePack quirks</span></span>

<span data-ttu-id="92189-303">使用 MessagePack 中心协议时，需要注意一些问题。</span><span class="sxs-lookup"><span data-stu-id="92189-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="92189-304">消息包区分大小写</span><span class="sxs-lookup"><span data-stu-id="92189-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="92189-305">MessagePack 协议区分大小写。</span><span class="sxs-lookup"><span data-stu-id="92189-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="92189-306">例如，请考虑以下 C# 类：</span><span class="sxs-lookup"><span data-stu-id="92189-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="92189-307">从 JavaScript 客户端发送时，必须使用`PascalCased`属性名称，因为大小写必须与 C# 类完全匹配。</span><span class="sxs-lookup"><span data-stu-id="92189-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="92189-308">例如：</span><span class="sxs-lookup"><span data-stu-id="92189-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="92189-309">使用`camelCased`名称不会正确绑定到 C# 类。</span><span class="sxs-lookup"><span data-stu-id="92189-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="92189-310">可以使用 属性`Key`为 MessagePack 属性指定其他名称来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="92189-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="92189-311">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="92189-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="92189-312">序列化/反序列化时不保留日期时间.Kind</span><span class="sxs-lookup"><span data-stu-id="92189-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="92189-313">MessagePack 协议不提供对`Kind`的值进行编码的方法。 `DateTime`</span><span class="sxs-lookup"><span data-stu-id="92189-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="92189-314">因此，当对日期进行反序列化时，MessagePack 中心协议假定传入日期为 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="92189-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="92189-315">如果您在本地时间使用`DateTime`值，我们建议您在发送之前转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="92189-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="92189-316">当您收到它们时，将它们从 UTC 转换为本地时间。</span><span class="sxs-lookup"><span data-stu-id="92189-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="92189-317">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="92189-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="92189-318">JavaScript 中的消息包不支持日期时间.MinValue</span><span class="sxs-lookup"><span data-stu-id="92189-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="92189-319">JavaScript 客户端使用的[msgpack5](https://github.com/mcollina/msgpack5)库不支持 MessagePack`timestamp96`中的类型。 SignalR</span><span class="sxs-lookup"><span data-stu-id="92189-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="92189-320">此类型用于编码非常大的日期值（过去很早就或将来非常远）。</span><span class="sxs-lookup"><span data-stu-id="92189-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="92189-321">的值`DateTime.MinValue``January 1, 0001`必须编码在`timestamp96`值中。</span><span class="sxs-lookup"><span data-stu-id="92189-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="92189-322">因此，不支持发送到`DateTime.MinValue`JavaScript 客户端。</span><span class="sxs-lookup"><span data-stu-id="92189-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="92189-323">当`DateTime.MinValue`JavaScript 客户端收到时，将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="92189-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="92189-324">通常，`DateTime.MinValue`用于编码"缺失"或`null`值。</span><span class="sxs-lookup"><span data-stu-id="92189-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="92189-325">如果需要在 MessagePack 中对该值进行编码，请使用空`DateTime`值 （`DateTime?`） 或编码`bool`单独的值，指示日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="92189-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="92189-326">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="92189-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="92189-327">"提前"编译环境中的消息包支持</span><span class="sxs-lookup"><span data-stu-id="92189-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="92189-328">.NET 客户端和服务器使用的[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="92189-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="92189-329">因此，默认情况下不支持使用"提前"编译（如 Xamarin iOS 或 Unity）的环境。</span><span class="sxs-lookup"><span data-stu-id="92189-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="92189-330">可以通过"预生成"序列化器/反序列化器代码在这些环境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="92189-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="92189-331">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="92189-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="92189-332">预先生成序列化器后，可以使用传递给 的配置委托注册它们`AddMessagePackProtocol`。</span><span class="sxs-lookup"><span data-stu-id="92189-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="92189-333">消息包中的类型检查更加严格</span><span class="sxs-lookup"><span data-stu-id="92189-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="92189-334">JSON 中心协议将在反序列化期间执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="92189-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="92189-335">例如，如果传入对象的属性值为数字 （），`{ foo: 42 }`但 .NET 类上的属性为类型`string`，则将转换该值。</span><span class="sxs-lookup"><span data-stu-id="92189-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="92189-336">但是，MessagePack 不执行此转换，并且将引发在服务器端日志（和控制台中）中可以看到的异常：</span><span class="sxs-lookup"><span data-stu-id="92189-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="92189-337">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="92189-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="92189-338">相关资源</span><span class="sxs-lookup"><span data-stu-id="92189-338">Related resources</span></span>

* [<span data-ttu-id="92189-339">入门</span><span class="sxs-lookup"><span data-stu-id="92189-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="92189-340">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="92189-341">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="92189-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
