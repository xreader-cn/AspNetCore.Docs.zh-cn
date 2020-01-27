---
title: 在 SignalR 中使用 MessagePack 集线器协议 ASP.NET Core
author: bradygaster
description: 将 MessagePack Hub 协议添加到 ASP.NET Core SignalR。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 1b01357233a9b95a5da052d92e30232c94e78a78
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727223"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="fe44f-103">在 SignalR 中使用 MessagePack 集线器协议 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe44f-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="fe44f-104">作者： [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="fe44f-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="fe44f-105">本文假定读者[熟悉入门中](xref:tutorials/signalr)介绍的主题。</span><span class="sxs-lookup"><span data-stu-id="fe44f-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="fe44f-106">什么是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="fe44f-106">What is MessagePack?</span></span>

<span data-ttu-id="fe44f-107">[MessagePack](https://msgpack.org/index.html)是一种快速且紧凑的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="fe44f-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="fe44f-108">当性能和带宽需要考虑时，它很有用，因为它会创建比[JSON](https://www.json.org/)更小的消息。</span><span class="sxs-lookup"><span data-stu-id="fe44f-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="fe44f-109">由于它是二进制格式，因此在查看网络跟踪和日志时会无法读取消息，除非这些字节是通过 MessagePack 分析器传递的。</span><span class="sxs-lookup"><span data-stu-id="fe44f-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="fe44f-110"> 提供对 MessagePack 格式的内置支持，并为客户端和服务器提供要使用的 Api。</span><span class="sxs-lookup"><span data-stu-id="fe44f-110"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="fe44f-111">在服务器上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="fe44f-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="fe44f-112">若要在服务器上启用 MessagePack 集线器协议，请在应用程序中安装 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 包。</span><span class="sxs-lookup"><span data-stu-id="fe44f-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="fe44f-113">在 Startup.cs 文件中，将 `AddMessagePackProtocol` 添加到 `AddSignalR` 调用，以在服务器上启用 MessagePack 支持。</span><span class="sxs-lookup"><span data-stu-id="fe44f-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="fe44f-114">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="fe44f-114">JSON is enabled by default.</span></span> <span data-ttu-id="fe44f-115">添加 MessagePack 可支持 JSON 和 MessagePack 客户端。</span><span class="sxs-lookup"><span data-stu-id="fe44f-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="fe44f-116">若要自定义 MessagePack 如何设置数据的格式，`AddMessagePackProtocol` 获取用于配置选项的委托。</span><span class="sxs-lookup"><span data-stu-id="fe44f-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="fe44f-117">在该委托中，`FormatterResolvers` 属性可用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="fe44f-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="fe44f-118">有关解析程序工作方式的详细信息，请访问[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)上的 MessagePack 库。</span><span class="sxs-lookup"><span data-stu-id="fe44f-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="fe44f-119">属性可用于要序列化的对象，以定义应如何处理它们。</span><span class="sxs-lookup"><span data-stu-id="fe44f-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="fe44f-120">在客户端上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="fe44f-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="fe44f-121">默认情况下，为支持的客户端启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="fe44f-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="fe44f-122">客户端只能支持一个协议。</span><span class="sxs-lookup"><span data-stu-id="fe44f-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="fe44f-123">添加 MessagePack 支持将替换任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="fe44f-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="fe44f-124">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="fe44f-124">.NET client</span></span>

<span data-ttu-id="fe44f-125">若要在 .NET 客户端中启用 MessagePack，请安装 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 包，并 `AddMessagePackProtocol` `HubConnectionBuilder`上调用。</span><span class="sxs-lookup"><span data-stu-id="fe44f-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="fe44f-126">此 `AddMessagePackProtocol` 调用采用一个委托来配置与服务器类似的选项。</span><span class="sxs-lookup"><span data-stu-id="fe44f-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="fe44f-127">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="fe44f-127">JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fe44f-128">`@microsoft/signalr-protocol-msgpack` npm 包提供对 JavaScript 客户端的 MessagePack 支持。</span><span class="sxs-lookup"><span data-stu-id="fe44f-128">MessagePack support for the JavaScript client is provided by the `@microsoft/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="fe44f-129">通过在命令行界面中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="fe44f-129">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fe44f-130">`@aspnet/signalr-protocol-msgpack` npm 包提供对 JavaScript 客户端的 MessagePack 支持。</span><span class="sxs-lookup"><span data-stu-id="fe44f-130">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="fe44f-131">通过在命令行界面中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="fe44f-131">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

<span data-ttu-id="fe44f-132">安装 npm 包后，可以通过 JavaScript 模块加载程序直接使用该模块，或通过引用以下文件将该模块导入到浏览器中：</span><span class="sxs-lookup"><span data-stu-id="fe44f-132">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fe44f-133">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="fe44f-133">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fe44f-134">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="fe44f-134">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

<span data-ttu-id="fe44f-135">在浏览器中，还必须引用 `msgpack5` 库。</span><span class="sxs-lookup"><span data-stu-id="fe44f-135">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="fe44f-136">使用 `<script>` 标记创建一个引用。</span><span class="sxs-lookup"><span data-stu-id="fe44f-136">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="fe44f-137">可在*node_modules \msgpack5\dist\msgpack5.js*找到库。</span><span class="sxs-lookup"><span data-stu-id="fe44f-137">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="fe44f-138">使用 `<script>` 元素时，顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="fe44f-138">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="fe44f-139">如果在*msgpack5*之前引用*signalr-protocol-msgpack* ，则在尝试与 MessagePack 连接时将出现错误。</span><span class="sxs-lookup"><span data-stu-id="fe44f-139">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="fe44f-140">*signalr*在*signalr-protocol-msgpack*之前也是必需的。</span><span class="sxs-lookup"><span data-stu-id="fe44f-140">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="fe44f-141">向 `HubConnectionBuilder` 中添加 `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` 会将客户端配置为在连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="fe44f-141">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="fe44f-142">目前，JavaScript 客户端上没有用于 MessagePack 协议的配置选项。</span><span class="sxs-lookup"><span data-stu-id="fe44f-142">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="fe44f-143">MessagePack 兼容</span><span class="sxs-lookup"><span data-stu-id="fe44f-143">MessagePack quirks</span></span>

<span data-ttu-id="fe44f-144">使用 MessagePack 集线器协议时，需要注意几个问题。</span><span class="sxs-lookup"><span data-stu-id="fe44f-144">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="fe44f-145">MessagePack 区分大小写</span><span class="sxs-lookup"><span data-stu-id="fe44f-145">MessagePack is case-sensitive</span></span>

<span data-ttu-id="fe44f-146">MessagePack 协议区分大小写。</span><span class="sxs-lookup"><span data-stu-id="fe44f-146">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="fe44f-147">例如，请看下面C#的类：</span><span class="sxs-lookup"><span data-stu-id="fe44f-147">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="fe44f-148">从 JavaScript 客户端发送时，必须使用 `PascalCased` 属性名称，因为大小写必须与C#类完全匹配。</span><span class="sxs-lookup"><span data-stu-id="fe44f-148">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="fe44f-149">例如：</span><span class="sxs-lookup"><span data-stu-id="fe44f-149">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="fe44f-150">使用 `camelCased` 名称不会正确绑定到C#类。</span><span class="sxs-lookup"><span data-stu-id="fe44f-150">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="fe44f-151">可以通过使用 `Key` 特性为 MessagePack 属性指定一个不同的名称来解决此情况。</span><span class="sxs-lookup"><span data-stu-id="fe44f-151">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="fe44f-152">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="fe44f-152">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="fe44f-153">序列化/反序列化时不保留 DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="fe44f-153">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="fe44f-154">MessagePack 协议不提供对 `DateTime`的 `Kind` 值进行编码的方法。</span><span class="sxs-lookup"><span data-stu-id="fe44f-154">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="fe44f-155">因此，在对日期进行反序列化时，MessagePack Hub 协议假设传入日期为 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="fe44f-155">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="fe44f-156">如果你在本地时间使用 `DateTime` 值，则建议在发送这些值之前将其转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="fe44f-156">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="fe44f-157">接收到本地时间时将它们从 UTC 转换为本地时间。</span><span class="sxs-lookup"><span data-stu-id="fe44f-157">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="fe44f-158">有关此限制的详细信息，请参阅 GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="fe44f-158">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="fe44f-159">JavaScript 中的 MessagePack 不支持 MinValue</span><span class="sxs-lookup"><span data-stu-id="fe44f-159">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="fe44f-160">SignalR JavaScript 客户端使用的[msgpack5](https://github.com/mcollina/msgpack5)库不支持 MessagePack 中的 `timestamp96` 类型。</span><span class="sxs-lookup"><span data-stu-id="fe44f-160">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="fe44f-161">此类型用于编码非常大的日期值（在过去或未来很大程度上）。</span><span class="sxs-lookup"><span data-stu-id="fe44f-161">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="fe44f-162">`DateTime.MinValue` 的值 `January 1, 0001` 必须用 `timestamp96` 值进行编码。</span><span class="sxs-lookup"><span data-stu-id="fe44f-162">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="fe44f-163">因此，不支持向 JavaScript 客户端发送 `DateTime.MinValue`。</span><span class="sxs-lookup"><span data-stu-id="fe44f-163">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="fe44f-164">当 JavaScript 客户端接收到 `DateTime.MinValue` 时，将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="fe44f-164">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="fe44f-165">通常，`DateTime.MinValue` 用于对 "缺少" 或 `null` 值进行编码。</span><span class="sxs-lookup"><span data-stu-id="fe44f-165">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="fe44f-166">如果需要在 MessagePack 中对该值进行编码，请使用可以为 null 的 `DateTime` 值（`DateTime?`），或对表示日期是否存在的单独 `bool` 值进行编码。</span><span class="sxs-lookup"><span data-stu-id="fe44f-166">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="fe44f-167">有关此限制的详细信息，请参阅 GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="fe44f-167">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="fe44f-168">"提前" 编译环境中的 MessagePack 支持</span><span class="sxs-lookup"><span data-stu-id="fe44f-168">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="fe44f-169">.NET 客户端和服务器使用的[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8)库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="fe44f-169">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="fe44f-170">因此，默认情况下，在使用 "提前" 编译（如 Xamarin iOS 或 Unity）的环境中，默认情况下不支持此方法。</span><span class="sxs-lookup"><span data-stu-id="fe44f-170">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="fe44f-171">可以通过 "预生成" 序列化程序/反序列化程序代码，在这些环境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="fe44f-171">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="fe44f-172">有关详细信息，请参阅[MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="fe44f-172">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="fe44f-173">预生成序列化程序后，可以使用传递给 `AddMessagePackProtocol`的配置委托注册它们：</span><span class="sxs-lookup"><span data-stu-id="fe44f-173">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="fe44f-174">类型检查在 MessagePack 中更加严格</span><span class="sxs-lookup"><span data-stu-id="fe44f-174">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="fe44f-175">JSON 集线器协议将在反序列化过程中执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="fe44f-175">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="fe44f-176">例如，如果传入的对象的属性值为数字（`{ foo: 42 }`），但 .NET 类的属性为类型 `string`，则将转换该值。</span><span class="sxs-lookup"><span data-stu-id="fe44f-176">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="fe44f-177">但是，MessagePack 不会执行此转换，并将引发可在服务器端日志中显示的异常（在控制台中）：</span><span class="sxs-lookup"><span data-stu-id="fe44f-177">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="fe44f-178">有关此限制的详细信息，请参阅 GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="fe44f-178">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="fe44f-179">相关资源</span><span class="sxs-lookup"><span data-stu-id="fe44f-179">Related resources</span></span>

* [<span data-ttu-id="fe44f-180">入门</span><span class="sxs-lookup"><span data-stu-id="fe44f-180">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="fe44f-181">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="fe44f-181">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="fe44f-182">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="fe44f-182">JavaScript client</span></span>](xref:signalr/javascript-client)
