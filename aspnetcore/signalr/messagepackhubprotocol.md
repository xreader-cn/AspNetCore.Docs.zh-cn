---
title: 使用中的 MessagePack Hub 协议 SignalR 进行 ASP.NET Core
author: bradygaster
description: 将 MessagePack Hub 协议添加到 ASP.NET Core SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 79a8c98d276738f687ef484795818897f18ceded
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755802"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="373d5-103">使用中的 MessagePack Hub 协议 SignalR 进行 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="373d5-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="373d5-104">本文假定读者[熟悉入门中](xref:tutorials/signalr)介绍的主题。</span><span class="sxs-lookup"><span data-stu-id="373d5-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="373d5-105">什么是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="373d5-105">What is MessagePack?</span></span>

<span data-ttu-id="373d5-106">[MessagePack](https://msgpack.org/index.html)是一种快速、精简的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="373d5-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="373d5-107">当性能和带宽需要考虑时，它很有用，因为它会创建比[JSON](https://www.json.org/)更小的消息。</span><span class="sxs-lookup"><span data-stu-id="373d5-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="373d5-108">在查看网络跟踪和日志时，不能读取二进制消息，除非这些字节是通过 MessagePack 分析器传递的。</span><span class="sxs-lookup"><span data-stu-id="373d5-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="373d5-109">提供对 MessagePack 格式的内置支持，并为客户端和服务器提供要使用的 Api。</span><span class="sxs-lookup"><span data-stu-id="373d5-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="373d5-110">在服务器上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="373d5-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="373d5-111">若要在服务器上启用 MessagePack 集线器协议，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在应用中安装包。</span><span class="sxs-lookup"><span data-stu-id="373d5-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="373d5-112">在 `Startup.ConfigureServices` 方法中，将添加 `AddMessagePackProtocol` 到在 `AddSignalR` 服务器上启用 MessagePack 支持的调用。</span><span class="sxs-lookup"><span data-stu-id="373d5-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="373d5-113">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="373d5-113">JSON is enabled by default.</span></span> <span data-ttu-id="373d5-114">添加 MessagePack 可支持 JSON 和 MessagePack 客户端。</span><span class="sxs-lookup"><span data-stu-id="373d5-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="373d5-115">若要自定义 MessagePack 如何设置数据的格式，请 `AddMessagePackProtocol` 使用委托来配置选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="373d5-116">在该委托中， `SerializerOptions` 属性可用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="373d5-117">有关解析程序工作方式的详细信息，请访问[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)上的 MessagePack 库。</span><span class="sxs-lookup"><span data-stu-id="373d5-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="373d5-118">属性可用于要序列化的对象，以定义应如何处理它们。</span><span class="sxs-lookup"><span data-stu-id="373d5-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="373d5-119">强烈建议查看[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)和应用建议的修补程序。</span><span class="sxs-lookup"><span data-stu-id="373d5-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="373d5-120">例如， `.WithSecurity(MessagePackSecurity.UntrustedData)` 在替换时调用 `SerializerOptions` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="373d5-121">在客户端上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="373d5-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="373d5-122">默认情况下，为支持的客户端启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="373d5-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="373d5-123">客户端只能支持一个协议。</span><span class="sxs-lookup"><span data-stu-id="373d5-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="373d5-124">添加 MessagePack 支持将替换任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="373d5-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="373d5-125">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-125">.NET client</span></span>

<span data-ttu-id="373d5-126">若要在 .NET 客户端中启用 MessagePack，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在上安装包并调用 `AddMessagePackProtocol` `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="373d5-127">此 `AddMessagePackProtocol` 调用采用一个委托来配置与服务器类似的选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="373d5-128">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-128">JavaScript client</span></span>

<span data-ttu-id="373d5-129">MessagePack 支持 JavaScript 客户端由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 包提供。</span><span class="sxs-lookup"><span data-stu-id="373d5-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="373d5-130">通过在命令行界面中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="373d5-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="373d5-131">安装 npm 包后，可以通过 JavaScript 模块加载程序直接使用该模块，或通过引用以下文件将该模块导入到浏览器中：</span><span class="sxs-lookup"><span data-stu-id="373d5-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="373d5-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="373d5-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="373d5-133">在浏览器中， `msgpack5` 还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="373d5-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="373d5-134">使用 `<script>` 标记创建引用。</span><span class="sxs-lookup"><span data-stu-id="373d5-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="373d5-135">可在*node_modules\msgpack5\dist\msgpack5.js*中找到库。</span><span class="sxs-lookup"><span data-stu-id="373d5-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="373d5-136">使用元素时 `<script>` ，顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="373d5-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="373d5-137">如果在*msgpack5.js*之前引用*signalr-protocol-msgpack.js* ，则在尝试使用 MessagePack 进行连接时将出现错误。</span><span class="sxs-lookup"><span data-stu-id="373d5-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="373d5-138">*signalr-protocol-msgpack.js*之前也需要*signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="373d5-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="373d5-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`如果添加到，则 `HubConnectionBuilder` 会将客户端配置为在连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="373d5-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="373d5-140">目前，JavaScript 客户端上没有用于 MessagePack 协议的配置选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="373d5-141">MessagePack 兼容</span><span class="sxs-lookup"><span data-stu-id="373d5-141">MessagePack quirks</span></span>

<span data-ttu-id="373d5-142">使用 MessagePack 集线器协议时，需要注意几个问题。</span><span class="sxs-lookup"><span data-stu-id="373d5-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="373d5-143">MessagePack 区分大小写</span><span class="sxs-lookup"><span data-stu-id="373d5-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="373d5-144">MessagePack 协议区分大小写。</span><span class="sxs-lookup"><span data-stu-id="373d5-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="373d5-145">例如，请考虑以下 c # 类：</span><span class="sxs-lookup"><span data-stu-id="373d5-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="373d5-146">从 JavaScript 客户端发送时，必须使用 `PascalCased` 属性名称，因为大小写必须与 c # 类完全匹配。</span><span class="sxs-lookup"><span data-stu-id="373d5-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="373d5-147">例如：</span><span class="sxs-lookup"><span data-stu-id="373d5-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="373d5-148">使用 `camelCased` 名称无法正确绑定到 c # 类。</span><span class="sxs-lookup"><span data-stu-id="373d5-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="373d5-149">可以通过使用 `Key` 属性为 MessagePack 属性指定不同的名称来解决此情况。</span><span class="sxs-lookup"><span data-stu-id="373d5-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="373d5-150">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="373d5-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="373d5-151">序列化/反序列化时不保留 DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="373d5-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="373d5-152">MessagePack 协议不提供对的值进行编码的方法 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="373d5-153">因此，在对日期进行反序列化时，如果为，则 MessagePack 集线器协议会转换为 UTC 格式， `DateTime.Kind` `DateTimeKind.Local` 否则它将不会触及时间并按原样传递它。</span><span class="sxs-lookup"><span data-stu-id="373d5-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="373d5-154">如果你使用的是 `DateTime` 值，则建议在发送这些值前将其转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="373d5-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="373d5-155">接收到本地时间时将它们从 UTC 转换为本地时间。</span><span class="sxs-lookup"><span data-stu-id="373d5-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="373d5-156">JavaScript 中的 MessagePack 不支持 MinValue</span><span class="sxs-lookup"><span data-stu-id="373d5-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="373d5-157">JavaScript 客户端使用的[msgpack5](https://github.com/mcollina/msgpack5)库 SignalR 不支持 `timestamp96` MessagePack 中的类型。</span><span class="sxs-lookup"><span data-stu-id="373d5-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="373d5-158">此类型用于编码非常大的日期值（在过去或未来很大程度上）。</span><span class="sxs-lookup"><span data-stu-id="373d5-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="373d5-159">的值 `DateTime.MinValue` 为 `January 1, 0001` ，必须在值中对其进行编码 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="373d5-160">因此， `DateTime.MinValue` 不支持向 JavaScript 客户端发送发送。</span><span class="sxs-lookup"><span data-stu-id="373d5-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="373d5-161">当 `DateTime.MinValue` JavaScript 客户端收到时，将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="373d5-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="373d5-162">通常， `DateTime.MinValue` 用于对 "缺失" 或值进行编码 `null` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="373d5-163">如果需要在 MessagePack 中对该值进行编码，请使用可以为 null 的 `DateTime` 值（ `DateTime?` ）或对 `bool` 指示日期是否存在的单独值进行编码。</span><span class="sxs-lookup"><span data-stu-id="373d5-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="373d5-164">有关此限制的详细信息，请参阅 GitHub 颁发[aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="373d5-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="373d5-165">"提前" 编译环境中的 MessagePack 支持</span><span class="sxs-lookup"><span data-stu-id="373d5-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="373d5-166">.NET 客户端和服务器使用的[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90)库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="373d5-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="373d5-167">因此，默认情况下，在使用 "提前" 编译（如 Xamarin iOS 或 Unity）的环境中，默认情况下不支持此方法。</span><span class="sxs-lookup"><span data-stu-id="373d5-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="373d5-168">可以通过 "预生成" 序列化程序/反序列化程序代码，在这些环境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="373d5-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="373d5-169">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)。</span><span class="sxs-lookup"><span data-stu-id="373d5-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="373d5-170">预生成序列化程序后，可以使用传递给的配置委托注册它们 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="373d5-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="373d5-171">类型检查在 MessagePack 中更加严格</span><span class="sxs-lookup"><span data-stu-id="373d5-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="373d5-172">JSON 集线器协议将在反序列化过程中执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="373d5-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="373d5-173">例如，如果传入的对象的属性值为数字（ `{ foo: 42 }` ），但 .net 类的属性的类型为 `string` ，则将转换该值。</span><span class="sxs-lookup"><span data-stu-id="373d5-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="373d5-174">但是，MessagePack 不会执行此转换，并将引发可在服务器端日志中显示的异常（在控制台中）：</span><span class="sxs-lookup"><span data-stu-id="373d5-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="373d5-175">有关此限制的详细信息，请参阅 GitHub 颁发[aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="373d5-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="373d5-176">相关资源</span><span class="sxs-lookup"><span data-stu-id="373d5-176">Related resources</span></span>

* [<span data-ttu-id="373d5-177">入门</span><span class="sxs-lookup"><span data-stu-id="373d5-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="373d5-178">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="373d5-179">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="373d5-180">本文假定读者[熟悉入门中](xref:tutorials/signalr)介绍的主题。</span><span class="sxs-lookup"><span data-stu-id="373d5-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="373d5-181">什么是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="373d5-181">What is MessagePack?</span></span>

<span data-ttu-id="373d5-182">[MessagePack](https://msgpack.org/index.html)是一种快速、精简的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="373d5-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="373d5-183">当性能和带宽需要考虑时，它很有用，因为它会创建比[JSON](https://www.json.org/)更小的消息。</span><span class="sxs-lookup"><span data-stu-id="373d5-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="373d5-184">在查看网络跟踪和日志时，不能读取二进制消息，除非这些字节是通过 MessagePack 分析器传递的。</span><span class="sxs-lookup"><span data-stu-id="373d5-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="373d5-185">提供对 MessagePack 格式的内置支持，并为客户端和服务器提供要使用的 Api。</span><span class="sxs-lookup"><span data-stu-id="373d5-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="373d5-186">在服务器上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="373d5-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="373d5-187">若要在服务器上启用 MessagePack 集线器协议，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在应用中安装包。</span><span class="sxs-lookup"><span data-stu-id="373d5-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="373d5-188">在 `Startup.ConfigureServices` 方法中，将添加 `AddMessagePackProtocol` 到在 `AddSignalR` 服务器上启用 MessagePack 支持的调用。</span><span class="sxs-lookup"><span data-stu-id="373d5-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="373d5-189">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="373d5-189">JSON is enabled by default.</span></span> <span data-ttu-id="373d5-190">添加 MessagePack 可支持 JSON 和 MessagePack 客户端。</span><span class="sxs-lookup"><span data-stu-id="373d5-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="373d5-191">若要自定义 MessagePack 如何设置数据的格式，请 `AddMessagePackProtocol` 使用委托来配置选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="373d5-192">在该委托中， `FormatterResolvers` 属性可用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="373d5-193">有关解析程序工作方式的详细信息，请访问[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)上的 MessagePack 库。</span><span class="sxs-lookup"><span data-stu-id="373d5-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="373d5-194">属性可用于要序列化的对象，以定义应如何处理它们。</span><span class="sxs-lookup"><span data-stu-id="373d5-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="373d5-195">强烈建议查看[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)和应用建议的修补程序。</span><span class="sxs-lookup"><span data-stu-id="373d5-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="373d5-196">例如，将 `MessagePackSecurity.Active` 静态属性设置为 `MessagePackSecurity.UntrustedData` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="373d5-197">设置 `MessagePackSecurity.Active` 需要手动安装[MessagePack 的1.9 版。](https://www.nuget.org/packages/MessagePack/1.9.3)</span><span class="sxs-lookup"><span data-stu-id="373d5-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="373d5-198">正在安装 `MessagePack` 版本为的 1.9. x 的升级 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="373d5-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="373d5-199">当未 `MessagePackSecurity.Active` 设置为时 `MessagePackSecurity.UntrustedData` ，恶意客户端可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="373d5-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="373d5-200">`MessagePackSecurity.Active`在中设置 `Program.Main` ，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="373d5-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="373d5-201">在客户端上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="373d5-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="373d5-202">默认情况下，为支持的客户端启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="373d5-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="373d5-203">客户端只能支持一个协议。</span><span class="sxs-lookup"><span data-stu-id="373d5-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="373d5-204">添加 MessagePack 支持将替换任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="373d5-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="373d5-205">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-205">.NET client</span></span>

<span data-ttu-id="373d5-206">若要在 .NET 客户端中启用 MessagePack，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在上安装包并调用 `AddMessagePackProtocol` `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="373d5-207">此 `AddMessagePackProtocol` 调用采用一个委托来配置与服务器类似的选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="373d5-208">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-208">JavaScript client</span></span>

<span data-ttu-id="373d5-209">MessagePack 支持 JavaScript 客户端由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 包提供。</span><span class="sxs-lookup"><span data-stu-id="373d5-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="373d5-210">通过在命令行界面中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="373d5-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="373d5-211">安装 npm 包后，可以通过 JavaScript 模块加载程序直接使用该模块，或通过引用以下文件将该模块导入到浏览器中：</span><span class="sxs-lookup"><span data-stu-id="373d5-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="373d5-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="373d5-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="373d5-213">在浏览器中， `msgpack5` 还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="373d5-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="373d5-214">使用 `<script>` 标记创建引用。</span><span class="sxs-lookup"><span data-stu-id="373d5-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="373d5-215">可在*node_modules\msgpack5\dist\msgpack5.js*中找到库。</span><span class="sxs-lookup"><span data-stu-id="373d5-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="373d5-216">使用元素时 `<script>` ，顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="373d5-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="373d5-217">如果在*msgpack5.js*之前引用*signalr-protocol-msgpack.js* ，则在尝试使用 MessagePack 进行连接时将出现错误。</span><span class="sxs-lookup"><span data-stu-id="373d5-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="373d5-218">*signalr-protocol-msgpack.js*之前也需要*signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="373d5-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="373d5-219">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`如果添加到，则 `HubConnectionBuilder` 会将客户端配置为在连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="373d5-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="373d5-220">目前，JavaScript 客户端上没有用于 MessagePack 协议的配置选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="373d5-221">MessagePack 兼容</span><span class="sxs-lookup"><span data-stu-id="373d5-221">MessagePack quirks</span></span>

<span data-ttu-id="373d5-222">使用 MessagePack 集线器协议时，需要注意几个问题。</span><span class="sxs-lookup"><span data-stu-id="373d5-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="373d5-223">MessagePack 区分大小写</span><span class="sxs-lookup"><span data-stu-id="373d5-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="373d5-224">MessagePack 协议区分大小写。</span><span class="sxs-lookup"><span data-stu-id="373d5-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="373d5-225">例如，请考虑以下 c # 类：</span><span class="sxs-lookup"><span data-stu-id="373d5-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="373d5-226">从 JavaScript 客户端发送时，必须使用 `PascalCased` 属性名称，因为大小写必须与 c # 类完全匹配。</span><span class="sxs-lookup"><span data-stu-id="373d5-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="373d5-227">例如：</span><span class="sxs-lookup"><span data-stu-id="373d5-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="373d5-228">使用 `camelCased` 名称无法正确绑定到 c # 类。</span><span class="sxs-lookup"><span data-stu-id="373d5-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="373d5-229">可以通过使用 `Key` 属性为 MessagePack 属性指定不同的名称来解决此情况。</span><span class="sxs-lookup"><span data-stu-id="373d5-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="373d5-230">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="373d5-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="373d5-231">序列化/反序列化时不保留 DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="373d5-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="373d5-232">MessagePack 协议不提供对的值进行编码的方法 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="373d5-233">因此，在对日期进行反序列化时，MessagePack Hub 协议假设传入日期为 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="373d5-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="373d5-234">如果使用的是 `DateTime` 本地时间的值，建议在发送之前将值转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="373d5-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="373d5-235">接收到本地时间时将它们从 UTC 转换为本地时间。</span><span class="sxs-lookup"><span data-stu-id="373d5-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="373d5-236">有关此限制的详细信息，请参阅 GitHub 颁发[aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="373d5-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="373d5-237">JavaScript 中的 MessagePack 不支持 MinValue</span><span class="sxs-lookup"><span data-stu-id="373d5-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="373d5-238">JavaScript 客户端使用的[msgpack5](https://github.com/mcollina/msgpack5)库 SignalR 不支持 `timestamp96` MessagePack 中的类型。</span><span class="sxs-lookup"><span data-stu-id="373d5-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="373d5-239">此类型用于编码非常大的日期值（在过去或未来很大程度上）。</span><span class="sxs-lookup"><span data-stu-id="373d5-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="373d5-240">的值 `DateTime.MinValue` 为 `January 1, 0001` ，必须在值中对其进行编码 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="373d5-241">因此， `DateTime.MinValue` 不支持向 JavaScript 客户端发送发送。</span><span class="sxs-lookup"><span data-stu-id="373d5-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="373d5-242">当 `DateTime.MinValue` JavaScript 客户端收到时，将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="373d5-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="373d5-243">通常， `DateTime.MinValue` 用于对 "缺失" 或值进行编码 `null` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="373d5-244">如果需要在 MessagePack 中对该值进行编码，请使用可以为 null 的 `DateTime` 值（ `DateTime?` ）或对 `bool` 指示日期是否存在的单独值进行编码。</span><span class="sxs-lookup"><span data-stu-id="373d5-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="373d5-245">有关此限制的详细信息，请参阅 GitHub 颁发[aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="373d5-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="373d5-246">"提前" 编译环境中的 MessagePack 支持</span><span class="sxs-lookup"><span data-stu-id="373d5-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="373d5-247">.NET 客户端和服务器使用的[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="373d5-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="373d5-248">因此，默认情况下，在使用 "提前" 编译（如 Xamarin iOS 或 Unity）的环境中，默认情况下不支持此方法。</span><span class="sxs-lookup"><span data-stu-id="373d5-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="373d5-249">可以通过 "预生成" 序列化程序/反序列化程序代码，在这些环境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="373d5-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="373d5-250">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="373d5-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="373d5-251">预生成序列化程序后，可以使用传递给的配置委托注册它们 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="373d5-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="373d5-252">类型检查在 MessagePack 中更加严格</span><span class="sxs-lookup"><span data-stu-id="373d5-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="373d5-253">JSON 集线器协议将在反序列化过程中执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="373d5-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="373d5-254">例如，如果传入的对象的属性值为数字（ `{ foo: 42 }` ），但 .net 类的属性的类型为 `string` ，则将转换该值。</span><span class="sxs-lookup"><span data-stu-id="373d5-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="373d5-255">但是，MessagePack 不会执行此转换，并将引发可在服务器端日志中显示的异常（在控制台中）：</span><span class="sxs-lookup"><span data-stu-id="373d5-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="373d5-256">有关此限制的详细信息，请参阅 GitHub 颁发[aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="373d5-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="373d5-257">相关资源</span><span class="sxs-lookup"><span data-stu-id="373d5-257">Related resources</span></span>

* [<span data-ttu-id="373d5-258">入门</span><span class="sxs-lookup"><span data-stu-id="373d5-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="373d5-259">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="373d5-260">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="373d5-261">本文假定读者[熟悉入门中](xref:tutorials/signalr)介绍的主题。</span><span class="sxs-lookup"><span data-stu-id="373d5-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="373d5-262">什么是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="373d5-262">What is MessagePack?</span></span>

<span data-ttu-id="373d5-263">[MessagePack](https://msgpack.org/index.html)是一种快速、精简的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="373d5-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="373d5-264">当性能和带宽需要考虑时，它很有用，因为它会创建比[JSON](https://www.json.org/)更小的消息。</span><span class="sxs-lookup"><span data-stu-id="373d5-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="373d5-265">在查看网络跟踪和日志时，不能读取二进制消息，除非这些字节是通过 MessagePack 分析器传递的。</span><span class="sxs-lookup"><span data-stu-id="373d5-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="373d5-266">提供对 MessagePack 格式的内置支持，并为客户端和服务器提供要使用的 Api。</span><span class="sxs-lookup"><span data-stu-id="373d5-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="373d5-267">在服务器上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="373d5-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="373d5-268">若要在服务器上启用 MessagePack 集线器协议，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在应用中安装包。</span><span class="sxs-lookup"><span data-stu-id="373d5-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="373d5-269">在 `Startup.ConfigureServices` 方法中，将添加 `AddMessagePackProtocol` 到在 `AddSignalR` 服务器上启用 MessagePack 支持的调用。</span><span class="sxs-lookup"><span data-stu-id="373d5-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="373d5-270">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="373d5-270">JSON is enabled by default.</span></span> <span data-ttu-id="373d5-271">添加 MessagePack 可支持 JSON 和 MessagePack 客户端。</span><span class="sxs-lookup"><span data-stu-id="373d5-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="373d5-272">若要自定义 MessagePack 如何设置数据的格式，请 `AddMessagePackProtocol` 使用委托来配置选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="373d5-273">在该委托中， `FormatterResolvers` 属性可用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="373d5-274">有关解析程序工作方式的详细信息，请访问[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)上的 MessagePack 库。</span><span class="sxs-lookup"><span data-stu-id="373d5-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="373d5-275">属性可用于要序列化的对象，以定义应如何处理它们。</span><span class="sxs-lookup"><span data-stu-id="373d5-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="373d5-276">强烈建议查看[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)和应用建议的修补程序。</span><span class="sxs-lookup"><span data-stu-id="373d5-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="373d5-277">例如，将 `MessagePackSecurity.Active` 静态属性设置为 `MessagePackSecurity.UntrustedData` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="373d5-278">设置 `MessagePackSecurity.Active` 需要手动安装[MessagePack 的1.9 版。](https://www.nuget.org/packages/MessagePack/1.9.3)</span><span class="sxs-lookup"><span data-stu-id="373d5-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="373d5-279">正在安装 `MessagePack` 版本为的 1.9. x 的升级 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="373d5-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="373d5-280">当未 `MessagePackSecurity.Active` 设置为时 `MessagePackSecurity.UntrustedData` ，恶意客户端可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="373d5-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="373d5-281">`MessagePackSecurity.Active`在中设置 `Program.Main` ，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="373d5-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="373d5-282">在客户端上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="373d5-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="373d5-283">默认情况下，为支持的客户端启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="373d5-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="373d5-284">客户端只能支持一个协议。</span><span class="sxs-lookup"><span data-stu-id="373d5-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="373d5-285">添加 MessagePack 支持将替换任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="373d5-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="373d5-286">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-286">.NET client</span></span>

<span data-ttu-id="373d5-287">若要在 .NET 客户端中启用 MessagePack，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在上安装包并调用 `AddMessagePackProtocol` `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="373d5-288">此 `AddMessagePackProtocol` 调用采用一个委托来配置与服务器类似的选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="373d5-289">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-289">JavaScript client</span></span>

<span data-ttu-id="373d5-290">MessagePack 支持 JavaScript 客户端由 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm 包提供。</span><span class="sxs-lookup"><span data-stu-id="373d5-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="373d5-291">通过在命令行界面中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="373d5-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="373d5-292">安装 npm 包后，可以通过 JavaScript 模块加载程序直接使用该模块，或通过引用以下文件将该模块导入到浏览器中：</span><span class="sxs-lookup"><span data-stu-id="373d5-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="373d5-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="373d5-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="373d5-294">在浏览器中， `msgpack5` 还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="373d5-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="373d5-295">使用 `<script>` 标记创建引用。</span><span class="sxs-lookup"><span data-stu-id="373d5-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="373d5-296">可在*node_modules\msgpack5\dist\msgpack5.js*中找到库。</span><span class="sxs-lookup"><span data-stu-id="373d5-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="373d5-297">使用元素时 `<script>` ，顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="373d5-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="373d5-298">如果在*msgpack5.js*之前引用*signalr-protocol-msgpack.js* ，则在尝试使用 MessagePack 进行连接时将出现错误。</span><span class="sxs-lookup"><span data-stu-id="373d5-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="373d5-299">*signalr-protocol-msgpack.js*之前也需要*signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="373d5-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="373d5-300">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`如果添加到，则 `HubConnectionBuilder` 会将客户端配置为在连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="373d5-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="373d5-301">目前，JavaScript 客户端上没有用于 MessagePack 协议的配置选项。</span><span class="sxs-lookup"><span data-stu-id="373d5-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="373d5-302">MessagePack 兼容</span><span class="sxs-lookup"><span data-stu-id="373d5-302">MessagePack quirks</span></span>

<span data-ttu-id="373d5-303">使用 MessagePack 集线器协议时，需要注意几个问题。</span><span class="sxs-lookup"><span data-stu-id="373d5-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="373d5-304">MessagePack 区分大小写</span><span class="sxs-lookup"><span data-stu-id="373d5-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="373d5-305">MessagePack 协议区分大小写。</span><span class="sxs-lookup"><span data-stu-id="373d5-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="373d5-306">例如，请考虑以下 c # 类：</span><span class="sxs-lookup"><span data-stu-id="373d5-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="373d5-307">从 JavaScript 客户端发送时，必须使用 `PascalCased` 属性名称，因为大小写必须与 c # 类完全匹配。</span><span class="sxs-lookup"><span data-stu-id="373d5-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="373d5-308">例如：</span><span class="sxs-lookup"><span data-stu-id="373d5-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="373d5-309">使用 `camelCased` 名称无法正确绑定到 c # 类。</span><span class="sxs-lookup"><span data-stu-id="373d5-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="373d5-310">可以通过使用 `Key` 属性为 MessagePack 属性指定不同的名称来解决此情况。</span><span class="sxs-lookup"><span data-stu-id="373d5-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="373d5-311">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="373d5-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="373d5-312">序列化/反序列化时不保留 DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="373d5-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="373d5-313">MessagePack 协议不提供对的值进行编码的方法 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="373d5-314">因此，在对日期进行反序列化时，MessagePack Hub 协议假设传入日期为 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="373d5-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="373d5-315">如果使用的是 `DateTime` 本地时间的值，建议在发送之前将值转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="373d5-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="373d5-316">接收到本地时间时将它们从 UTC 转换为本地时间。</span><span class="sxs-lookup"><span data-stu-id="373d5-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="373d5-317">有关此限制的详细信息，请参阅 GitHub 颁发[aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="373d5-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="373d5-318">JavaScript 中的 MessagePack 不支持 MinValue</span><span class="sxs-lookup"><span data-stu-id="373d5-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="373d5-319">JavaScript 客户端使用的[msgpack5](https://github.com/mcollina/msgpack5)库 SignalR 不支持 `timestamp96` MessagePack 中的类型。</span><span class="sxs-lookup"><span data-stu-id="373d5-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="373d5-320">此类型用于编码非常大的日期值（在过去或未来很大程度上）。</span><span class="sxs-lookup"><span data-stu-id="373d5-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="373d5-321">的值 `DateTime.MinValue` `January 1, 0001` 必须在值中进行编码 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="373d5-322">因此， `DateTime.MinValue` 不支持向 JavaScript 客户端发送发送。</span><span class="sxs-lookup"><span data-stu-id="373d5-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="373d5-323">当 `DateTime.MinValue` JavaScript 客户端收到时，将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="373d5-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="373d5-324">通常， `DateTime.MinValue` 用于对 "缺失" 或值进行编码 `null` 。</span><span class="sxs-lookup"><span data-stu-id="373d5-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="373d5-325">如果需要在 MessagePack 中对该值进行编码，请使用可以为 null 的 `DateTime` 值（ `DateTime?` ）或对 `bool` 指示日期是否存在的单独值进行编码。</span><span class="sxs-lookup"><span data-stu-id="373d5-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="373d5-326">有关此限制的详细信息，请参阅 GitHub 颁发[aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="373d5-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="373d5-327">"提前" 编译环境中的 MessagePack 支持</span><span class="sxs-lookup"><span data-stu-id="373d5-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="373d5-328">.NET 客户端和服务器使用的[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80)库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="373d5-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="373d5-329">因此，默认情况下，在使用 "提前" 编译（如 Xamarin iOS 或 Unity）的环境中，默认情况下不支持此方法。</span><span class="sxs-lookup"><span data-stu-id="373d5-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="373d5-330">可以通过 "预生成" 序列化程序/反序列化程序代码，在这些环境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="373d5-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="373d5-331">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="373d5-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="373d5-332">预生成序列化程序后，可以使用传递给的配置委托注册它们 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="373d5-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="373d5-333">类型检查在 MessagePack 中更加严格</span><span class="sxs-lookup"><span data-stu-id="373d5-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="373d5-334">JSON 集线器协议将在反序列化过程中执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="373d5-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="373d5-335">例如，如果传入的对象的属性值为数字（ `{ foo: 42 }` ），但 .net 类的属性的类型为 `string` ，则将转换该值。</span><span class="sxs-lookup"><span data-stu-id="373d5-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="373d5-336">但是，MessagePack 不会执行此转换，并将引发可在服务器端日志中显示的异常（在控制台中）：</span><span class="sxs-lookup"><span data-stu-id="373d5-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="373d5-337">有关此限制的详细信息，请参阅 GitHub 颁发[aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="373d5-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="373d5-338">相关资源</span><span class="sxs-lookup"><span data-stu-id="373d5-338">Related resources</span></span>

* [<span data-ttu-id="373d5-339">入门</span><span class="sxs-lookup"><span data-stu-id="373d5-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="373d5-340">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="373d5-341">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="373d5-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
