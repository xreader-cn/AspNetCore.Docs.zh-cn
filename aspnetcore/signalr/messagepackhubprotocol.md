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
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400666"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="bd3de-103">使用 ASP.NET Core MessagePack 中心协议中 SignalR</span><span class="sxs-lookup"><span data-stu-id="bd3de-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="bd3de-104">通过[brennan，第 Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="bd3de-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="bd3de-105">本文假定读者熟悉中所涉及的主题[开始](xref:tutorials/signalr)。</span><span class="sxs-lookup"><span data-stu-id="bd3de-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="bd3de-106">MessagePack 是什么？</span><span class="sxs-lookup"><span data-stu-id="bd3de-106">What is MessagePack?</span></span>

<span data-ttu-id="bd3de-107">[MessagePack](https://msgpack.org/index.html)是快速和精简的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="bd3de-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="bd3de-108">性能和带宽是一个问题，因为它将创建较小的消息相比时很有用[JSON](https://www.json.org/)。</span><span class="sxs-lookup"><span data-stu-id="bd3de-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="bd3de-109">因为它是以二进制格式，则消息是不可读，查看网络跟踪和日志，除非通过 MessagePack 分析器传递字节时。</span><span class="sxs-lookup"><span data-stu-id="bd3de-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="bd3de-110">SignalR 为 MessagePack 格式中，提供内置支持，并提供 Api，可用于要使用的客户端和服务器。</span><span class="sxs-lookup"><span data-stu-id="bd3de-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="bd3de-111">在服务器上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="bd3de-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="bd3de-112">若要启用 MessagePack 中心协议的服务器上，安装`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`应用程序中的包。</span><span class="sxs-lookup"><span data-stu-id="bd3de-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="bd3de-113">在 Startup.cs 文件中添加`AddMessagePackProtocol`到`AddSignalR`调用，以启用 MessagePack 支持在服务器上的。</span><span class="sxs-lookup"><span data-stu-id="bd3de-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="bd3de-114">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="bd3de-114">JSON is enabled by default.</span></span> <span data-ttu-id="bd3de-115">添加 MessagePack，对 JSON 和 MessagePack 客户端的支持。</span><span class="sxs-lookup"><span data-stu-id="bd3de-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="bd3de-116">若要自定义 MessagePack 设置你的数据的格式`AddMessagePackProtocol`接受委托，用于配置选项。</span><span class="sxs-lookup"><span data-stu-id="bd3de-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="bd3de-117">在该委托`FormatterResolvers`属性可以用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="bd3de-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="bd3de-118">冲突解决程序的工作原理的详细信息，请访问位于的 MessagePack library [MessagePack CSharp](https://github.com/neuecc/MessagePack-CSharp)。</span><span class="sxs-lookup"><span data-stu-id="bd3de-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="bd3de-119">要序列化定义应如何处理它们的对象上，可以使用属性。</span><span class="sxs-lookup"><span data-stu-id="bd3de-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="bd3de-120">在客户端上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="bd3de-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="bd3de-121">支持的客户端的情况下，默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="bd3de-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="bd3de-122">客户端可以只支持单个协议。</span><span class="sxs-lookup"><span data-stu-id="bd3de-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="bd3de-123">添加 MessagePack 支持会将为任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="bd3de-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="bd3de-124">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="bd3de-124">.NET client</span></span>

<span data-ttu-id="bd3de-125">若要启用 MessagePack.NET 客户端中的，安装`Microsoft.AspNetCore.SignalR.Protocols.MessagePack`包并调用`AddMessagePackProtocol`上`HubConnectionBuilder`。</span><span class="sxs-lookup"><span data-stu-id="bd3de-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="bd3de-126">这`AddMessagePackProtocol`调用接受委托用于配置服务器一样的选项。</span><span class="sxs-lookup"><span data-stu-id="bd3de-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="bd3de-127">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="bd3de-127">JavaScript client</span></span>

<span data-ttu-id="bd3de-128">MessagePack 支持 JavaScript 客户端提供的`@aspnet/signalr-protocol-msgpack`npm 包。</span><span class="sxs-lookup"><span data-stu-id="bd3de-128">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="bd3de-129">安装 npm 包之后, 该模块可以使用 JavaScript 模块加载程序通过直接或通过引用导入到浏览器 *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 文件。</span><span class="sxs-lookup"><span data-stu-id="bd3de-129">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* file.</span></span> <span data-ttu-id="bd3de-130">在浏览器`msgpack5`还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="bd3de-130">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="bd3de-131">使用`<script>`标记创建的引用。</span><span class="sxs-lookup"><span data-stu-id="bd3de-131">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="bd3de-132">可以在找到的库*node_modules\msgpack5\dist\msgpack5.js*。</span><span class="sxs-lookup"><span data-stu-id="bd3de-132">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="bd3de-133">当使用`<script>`元素的顺序非常重要。</span><span class="sxs-lookup"><span data-stu-id="bd3de-133">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="bd3de-134">如果*signalr 协议 msgpack.js*引用之前*msgpack5.js*，尝试使用 MessagePack 连接时出错。</span><span class="sxs-lookup"><span data-stu-id="bd3de-134">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="bd3de-135">*signalr.js*也需要前*signalr 协议 msgpack.js*。</span><span class="sxs-lookup"><span data-stu-id="bd3de-135">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="bd3de-136">添加`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`到`HubConnectionBuilder`将配置客户端连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="bd3de-136">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="bd3de-137">在此期间，有 JavaScript 客户端上的 MessagePack 协议没有配置选项。</span><span class="sxs-lookup"><span data-stu-id="bd3de-137">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="bd3de-138">MessagePack quirks</span><span class="sxs-lookup"><span data-stu-id="bd3de-138">MessagePack quirks</span></span>

<span data-ttu-id="bd3de-139">有几个问题需要注意的使用 MessagePack 中心协议时。</span><span class="sxs-lookup"><span data-stu-id="bd3de-139">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="bd3de-140">MessagePack 是区分大小写</span><span class="sxs-lookup"><span data-stu-id="bd3de-140">MessagePack is case-sensitive</span></span>

<span data-ttu-id="bd3de-141">MessagePack 协议是区分大小写。</span><span class="sxs-lookup"><span data-stu-id="bd3de-141">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="bd3de-142">例如，请考虑以下C#类：</span><span class="sxs-lookup"><span data-stu-id="bd3de-142">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="bd3de-143">在从 JavaScript 客户端发送时，必须使用`PascalCased`属性名称，由于大小写必须与匹配C#完全类。</span><span class="sxs-lookup"><span data-stu-id="bd3de-143">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="bd3de-144">例如：</span><span class="sxs-lookup"><span data-stu-id="bd3de-144">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="bd3de-145">使用`camelCased`名称不会正确地绑定到C#类。</span><span class="sxs-lookup"><span data-stu-id="bd3de-145">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="bd3de-146">可以通过使用解决此`Key`属性来指定 MessagePack 属性不同的名称。</span><span class="sxs-lookup"><span data-stu-id="bd3de-146">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="bd3de-147">有关详细信息，请参阅[MessagePack CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="bd3de-147">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="bd3de-148">序列化/反序列化时，不会保留 DateTime.Kind</span><span class="sxs-lookup"><span data-stu-id="bd3de-148">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="bd3de-149">MessagePack 协议不提供方法来进行编码`Kind`的值`DateTime`。</span><span class="sxs-lookup"><span data-stu-id="bd3de-149">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="bd3de-150">因此，当反序列化日期，MessagePack 中心协议假定传入日期均采用 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="bd3de-150">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="bd3de-151">如果您正在使用`DateTime`本地时间中的值，我们建议将它们发送之前将转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="bd3de-151">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="bd3de-152">它们从 UTC 转换为本地时间时你收到它们。</span><span class="sxs-lookup"><span data-stu-id="bd3de-152">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="bd3de-153">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR # 2632年](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="bd3de-153">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="bd3de-154">MessagePack 在 JavaScript 中不支持 DateTime.MinValue</span><span class="sxs-lookup"><span data-stu-id="bd3de-154">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="bd3de-155">[Msgpack5](https://github.com/mcollina/msgpack5)使用 SignalR JavaScript 客户端库不支持`timestamp96`MessagePack 中的类型。</span><span class="sxs-lookup"><span data-stu-id="bd3de-155">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="bd3de-156">此类型用于编码非常大的日期值 （无论是很早在过去或将来很久）。</span><span class="sxs-lookup"><span data-stu-id="bd3de-156">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="bd3de-157">值`DateTime.MinValue`是`January 1, 0001`其必须以编码`timestamp96`值。</span><span class="sxs-lookup"><span data-stu-id="bd3de-157">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="bd3de-158">由于此问题，发送`DateTime.MinValue`向 JavaScript 客户端不支持。</span><span class="sxs-lookup"><span data-stu-id="bd3de-158">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="bd3de-159">当`DateTime.MinValue`收到通过 JavaScript 客户端，引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="bd3de-159">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="bd3de-160">通常情况下，`DateTime.MinValue`用于编码"缺少"或`null`值。</span><span class="sxs-lookup"><span data-stu-id="bd3de-160">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="bd3de-161">如果你需要对 MessagePack 中的值进行编码，使用一个可以为 null`DateTime`值 (`DateTime?`) 或编码单独`bool`值，该值指示日期是否存在。</span><span class="sxs-lookup"><span data-stu-id="bd3de-161">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="bd3de-162">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR # 2228年](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="bd3de-162">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="bd3de-163">在"预时间的"编译环境中的 MessagePack 支持</span><span class="sxs-lookup"><span data-stu-id="bd3de-163">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="bd3de-164">[MessagePack CSharp](https://github.com/neuecc/MessagePack-CSharp)使用.NET 客户端和服务器库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="bd3de-164">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="bd3de-165">因此，它不支持默认情况下，在使用"预时间的"编译 （如 Xamarin iOS 或 Unity） 的环境。</span><span class="sxs-lookup"><span data-stu-id="bd3de-165">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="bd3de-166">就可以在这些环境中使用 MessagePack，通过"预生成"的序列化程序/反序列化程序代码。</span><span class="sxs-lookup"><span data-stu-id="bd3de-166">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="bd3de-167">有关详细信息，请参阅[MessagePack CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="bd3de-167">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="bd3de-168">一旦预生成序列化程序，可以注册它们使用传递给配置委托`AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="bd3de-168">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="bd3de-169">类型检查都在 MessagePack 更严格</span><span class="sxs-lookup"><span data-stu-id="bd3de-169">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="bd3de-170">JSON 中心协议将在反序列化期间执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="bd3de-170">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="bd3de-171">例如，如果传入的对象的属性值是一个数字 (`{ foo: 42 }`) 上的.NET 类的属性属于类型，但`string`，值将被转换。</span><span class="sxs-lookup"><span data-stu-id="bd3de-171">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="bd3de-172">但是，MessagePack 不会执行此转换，并将引发异常，可以看到，在服务器端日志中 （并在控制台中）：</span><span class="sxs-lookup"><span data-stu-id="bd3de-172">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="bd3de-173">有关此限制的详细信息，请参阅 GitHub 问题[aspnet/SignalR # 2937年](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="bd3de-173">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="bd3de-174">相关资源</span><span class="sxs-lookup"><span data-stu-id="bd3de-174">Related resources</span></span>

* [<span data-ttu-id="bd3de-175">入门</span><span class="sxs-lookup"><span data-stu-id="bd3de-175">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="bd3de-176">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="bd3de-176">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="bd3de-177">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="bd3de-177">JavaScript client</span></span>](xref:signalr/javascript-client)
