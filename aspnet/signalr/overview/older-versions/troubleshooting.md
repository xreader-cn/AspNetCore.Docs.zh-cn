---
uid: signalr/overview/older-versions/troubleshooting
title: SignalR 疑难解答 (SignalR 1.x) |Microsoft Docs
author: bradygaster
description: 本文介绍开发 SignalR 应用程序的常见问题。
ms.author: bradyg
ms.date: 06/05/2013
ms.assetid: 347210ba-c452-4feb-886f-b51d89f58971
msc.legacyurl: /signalr/overview/older-versions/troubleshooting
msc.type: authoredcontent
ms.openlocfilehash: d485829fb60b32b063405f37ae9c6c278bf7255b
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836267"
---
<a name="signalr-troubleshooting-signalr-1x"></a>SignalR 疑难解答 (SignalR 1.x)
====================
通过[Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本文档介绍使用 signalr 实现的常见故障排除问题。


本文档包含以下各节。

- [以无提示方式调用客户端和服务器之间的方法，则会失败](#connection)
- [其他的连接问题](#other)
- [编译和服务器端错误](#server)
- [Visual Studio 问题](#vs)
- [Internet 信息服务的问题](#iis)
- [Azure 问题](#azure)

<a id="connection"></a>

## <a name="calling-methods-between-the-client-and-server-silently-fails"></a>以无提示方式调用客户端和服务器之间的方法，则会失败

本部分介绍的方法调用失败但不会有意义的错误消息的客户端和服务器之间可能的原因。 在 SignalR 应用程序中，服务器具有任何信息的方法的客户端实现;当服务器调用客户端方法，方法名称和参数数据发送到客户端，并执行的方法，仅当它存在于指定的服务器的格式。 如果没有匹配的方法找到在客户端上没有任何反应，并在服务器上引发任何错误消息。

若要进一步调查您尚未进行调用的客户端方法，您可以启用日志记录之前调用的中心查看哪些调用 start 方法来自服务器的选项。 若要启用日志记录中的 JavaScript 应用程序，请参阅[如何启用客户端的日志记录 （JavaScript 客户端版本）](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging)。 若要启用.NET 客户端应用程序中的日志记录，请参阅[如何启用客户端的日志记录 （.NET 客户端版本）](../guide-to-the-api/hubs-api-guide-net-client.md#logging)。

### <a name="misspelled-method-incorrect-method-signature-or-incorrect-hub-name"></a>拼写错误的方法、 不正确的方法签名或不正确的中心名称

如果名称或调用方法的签名与客户端上适当的方法不完全匹配，则调用将失败。 验证服务器调用的方法名称与在客户端上方法的名称相匹配。 此外，SignalR 会创建集线器代理使用 camel 大小写的方法，以根据在 JavaScript 中，因此调用的方法`SendMessage`在服务器上则将调用`sendMessage`客户端代理中。 如果使用`HubName`属性在服务器端代码中，验证所使用的名称与用来在客户端上创建中心的名称相匹配。 如果不使用`HubName`属性，请验证是否驼峰式大小写，而不是 ChatHub chatHub 如 JavaScript 客户端中的中心的名称。

### <a name="duplicate-method-name-on-client"></a>客户端上的方法名称重复

验证仅大小写上不同的客户端上有了重复的方法。 如果客户端应用程序具有一个名为方法`sendMessage`，验证是否未还调用的方法`SendMessage`也。

### <a name="missing-json-parser-on-the-client"></a>在客户端上缺少 JSON 分析器

SignalR 需要 JSON 分析器必须存在要序列化的服务器和客户端之间的调用。 如果您的客户端不具有内置的 JSON 分析器 （如 Internet Explorer 7)，你将需要包含一个应用程序中。 您可以下载 JSON 分析器[此处](http://nuget.org/packages/json2)。

### <a name="mixing-hub-and-persistentconnection-syntax"></a>混合使用中心 PersistentConnection 语法

SignalR 使用两个通信模型：中心和 PersistentConnections。 调用这两种通信模型的语法中是不同的客户端代码。 如果在服务器代码中添加了一个中心，验证所有客户端代码中使用正确的中心语法。

**在 JavaScript 客户端中创建 PersistentConnection 的 JavaScript 客户端代码**

[!code-javascript[Main](troubleshooting/samples/sample1.js)]

**在 Javascript 客户端创建集线器代理的 JavaScript 客户端代码**

[!code-javascript[Main](troubleshooting/samples/sample2.js)]

**将路由映射到 PersistentConnection 的 C# 服务器代码**

[!code-csharp[Main](troubleshooting/samples/sample3.cs)]

**如果有多个应用程序映射路由到集线器，或多个中心的 C# 服务器代码**

[!code-csharp[Main](troubleshooting/samples/sample4.cs)]

### <a name="connection-started-before-subscriptions-are-added"></a>添加订阅之前启动的连接

如果可以从服务器调用的方法添加到代理之前启动中心的连接，则将不会收到消息。 以下 JavaScript 代码将不会正确启动中心：

**将不允许中心以接收的消息的不正确的 JavaScript 客户端代码**

[!code-javascript[Main](troubleshooting/samples/sample5.js)]

相反，将方法订阅添加调用开始之前：

**正确地将订阅添加到集线器的 JavaScript 客户端代码**

[!code-javascript[Main](troubleshooting/samples/sample6.js)]

### <a name="missing-method-name-on-the-hub-proxy"></a>缺少的集线器代理的方法名称

验证在服务器上定义的方法在客户端上订阅了。 即使服务器定义的方法，则它必须仍将添加到客户端代理。 方法可以按以下方式添加到客户端代理 (请注意，该方法添加到`client`的中心，而不是中心直接成员):

**将方法添加到集线器代理的 JavaScript 客户端代码**

[!code-javascript[Main](troubleshooting/samples/sample7.js)]

### <a name="hub-or-hub-methods-not-declared-as-public"></a>中心或未声明为公共的集线器方法

若要在客户端上可见，中心实现和方法必须声明为`public`。

### <a name="accessing-hub-from-a-different-application"></a>从不同的应用程序访问中心

SignalR 集线器仅可以通过实现 SignalR 客户端的应用程序访问。 SignalR 不能与其他通信库 （例如 SOAP 或 WCF web 服务。） 进行互操作如果没有可用于您的目标平台的无 SignalR 客户端，您不能直接访问服务器的终结点。

### <a name="manually-serializing-data"></a>手动序列化数据

SignalR 将自动使用 JSON 进行序列化您的方法参数-这不需要自行执行。

### <a name="remote-hub-method-not-executed-on-client-in-ondisconnected-function"></a>OnDisconnected 函数中的客户端上不执行远程中心方法

此行为是有意安排的。 当`OnDisconnected`是调用，已进入中心`Disconnected`状态，不允许进一步集线器方法调用。

**正确 OnDisconnected 事件中执行代码的 C# 服务器代码**

[!code-csharp[Main](troubleshooting/samples/sample8.cs)]

### <a name="connection-limit-reached"></a>已达到连接限制

当在 Windows 7 等客户端操作系统上使用 IIS 的完整版本，10 连接限制。 当使用客户端操作系统，改用 IIS Express 以避免此限制。

### <a name="cross-domain-connection-not-set-up-properly"></a>未正确设置跨域连接

如果跨域连接 （连接为其 SignalR URL 不是在托管的页面所在的域中） 未正确设置、 连接而不显示错误消息可能会失败。 有关如何启用跨域通信的信息，请参阅[如何建立跨域连接](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain)。

### <a name="connection-using-ntlm-active-directory-not-working-in-net-client"></a>连接使用 NTLM (Active Directory) 中.NET 客户端无法正常工作

如果连接未正确配置，使用域安全的.NET 客户端应用程序中的连接可能会失败。 若要在域环境中使用 SignalR，请按如下所示设置必要的连接属性：

**实现连接凭据的 C# 客户端代码**

[!code-csharp[Main](troubleshooting/samples/sample9.cs)]

<a id="other"></a>

## <a name="other-connection-issues"></a>其他的连接问题

本部分介绍的原因和解决方案的具体的症状或在连接期间发生的错误消息。

### <a name="start-must-be-called-before-data-can-be-sent-error"></a>"可以发送数据之前，必须调用 start"错误

如果代码引用 SignalR 对象之前启动连接，则通常会出现此错误。 处理程序和类似的绑定，将在服务器上定义的调用方法必须添加在连接完成之后。 请注意，在调用`Start`是异步的因此代码调用可能会在它之前执行后完成。 连接完全启动后添加处理程序的最佳方法是将它们放入作为参数传递给启动方法的回调函数：

**正确添加了引用 SignalR 对象的事件处理程序的 JavaScript 客户端代码**

[!code-javascript[Main](troubleshooting/samples/sample10.js?highlight=1)]

如果连接停止时仍引用 SignalR 对象，则还将看到此错误。

### <a name="301-moved-permanently-or-302-moved-temporarily-error"></a>"301 已永久移动"或者"302 已暂时移动"错误

如果项目包含一个名为 SignalR 中，将会影响自动创建代理的文件夹，则可能出现此错误。 若要避免此错误，不使用名为的文件夹`SignalR`中你的应用程序或关闭启用自动代理生成。 请参阅[生成代理和它为您完成](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy)的更多详细信息。

### <a name="403-forbidden-error-in-net-or-silverlight-client"></a>.NET 或 Silverlight 客户端中的"403 禁止访问"错误

在跨域通信未正确启用跨域环境中可能出现此错误。 有关如何启用跨域通信的信息，请参阅[如何建立跨域连接](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain)。 若要建立 Silverlight 客户端中的跨域连接，请参阅[Silverlight 客户端的跨域连接](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain)。

### <a name="404-not-found-error"></a>"404 未找到"错误

有多个此问题的原因。 验证以下所有内容的：

- **集线器代理地址引用的格式不正确：** 如果对生成的中心代理地址的引用的格式不正确，则通常会出现此错误。 验证可以正确地完成对中心地址的引用。 请参阅[如何引用动态生成的代理](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy)有关详细信息。
- **将路由添加到应用程序，然后添加中心路由：** 如果应用程序使用其他路由，请验证添加的第一个路由是对的调用`MapHubs`。

### <a name="500-internal-server-error"></a>"500 内部服务器错误"

这是一个非常一般性错误，可能有各种不同的原因。 错误的详细信息应出现在服务器的事件日志，或可通过调试服务器查找。 可通过启用详细错误的服务器上获取更详细的错误信息。 有关详细信息，请参阅[如何处理错误，Hub 类](../guide-to-the-api/hubs-api-guide-server.md#handleErrors)。

### <a name="typeerror-lthubtypegt-is-undefined-error"></a>"TypeError: &lt;hubType&gt;未定义"错误

如果此错误将导致调用`MapHubs`进行不正确。 请参阅[如何注册 SignalR 路由和配置 SignalR 选项](../guide-to-the-api/hubs-api-guide-server.md#route)有关详细信息。

### <a name="jsonserializationexception-was-unhandled-by-user-code"></a>用户代码未处理 JsonSerializationException

验证发送到你的方法的参数不包括非可序列化类型 （如文件句柄或数据库连接）。 如果你需要在不想要发送到客户端 （不管是安全或者序列化的原因），使用服务器端对象上使用成员`JSONIgnore`属性。

### <a name="protocol-error-unknown-transport-error"></a>"协议错误：未知的传输"错误

如果客户端不支持 SignalR 使用的传输，则可能出现此错误。 请参阅[传输和回退](../getting-started/introduction-to-signalr.md#transports)与 SignalR 可以在其上使用浏览器的信息。

### <a name="javascript-hub-proxy-generation-has-been-disabled"></a>"JavaScript 中心代理生成已禁用。"

如果将发生此错误`DisableJavaScriptProxies`时还包括对在动态生成的代理的引用设置`signalr/hubs`。 有关手动创建代理的详细信息，请参阅[生成的代理和它为您完成](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy)。

### <a name="the-connection-id-is-in-the-incorrect-format-or-the-user-identity-cannot-change-during-an-active-signalr-connection-error"></a>"连接 ID 的格式不正确"或"用户标识不能更改期间的活动的 SignalR 连接"错误

如果正在使用身份验证，并且客户端已注销，停止连接之前，可能会出现此错误。 解决方案是停止 SignalR 连接之前注销客户端。

### <a name="uncaught-error-signalr-jquery-not-found-please-ensure-jquery-is-referenced-before-the-signalrjs-file-error"></a>"未捕获的错误：SignalR： 找不到 jQuery。 请确保前 SignalR.js 文件引用 jQuery"错误

SignalR JavaScript 客户端需要 jQuery 来运行。 验证你对 jQuery 的引用正确，所用的路径有效，以及对 jQuery 的引用是对 SignalR 的引用之前。

### <a name="uncaught-typeerror-cannot-read-property-ltpropertygt-of-undefined-error"></a>"未捕获的 TypeError:无法读取属性&lt;属性&gt;未定义的"错误

防止不 jQuery 或引用正确的中心代理产生此错误。 验证引用与 jQuery 和中心代理正确，所用的路径有效，以及对 jQuery 的引用是对中心代理的引用之前。 集线器代理的默认引用应如下所示：

**HTML 客户端代码的正确引用中心代理**

[!code-html[Main](troubleshooting/samples/sample11.html)]

### <a name="runtimebinderexception-was-unhandled-by-user-code-error"></a>"通过用户代码未处理 RuntimeBinderException"错误

可能会发生此错误时的不正确的重载`Hub.On`使用。 如果该方法具有返回值，则必须作为泛型类型参数指定的返回类型：

**（无需生成的代理） 客户端上定义的方法**

[!code-html[Main](troubleshooting/samples/sample12.html?highlight=1)]

### <a name="connection-id-is-inconsistent-or-connection-breaks-between-page-loads"></a>连接 ID 不一致或页面加载之间的连接中断

此行为是有意安排的。 由于集线器对象托管在 page 对象中，中心被销毁时刷新该页面。 多页应用程序需要维护用户与连接 Id 之间的关联，以便将页面加载之间保持一致。 可以在服务器上存储的连接 Id`ConcurrentDictionary`对象或数据库。

### <a name="value-cannot-be-null-error"></a>"值不能为 null"错误

当前不支持具有可选参数的服务器端方法;如果省略了可选参数，则该方法将失败。 有关详细信息，请参阅[可选参数](https://github.com/SignalR/SignalR/issues/324)。

### <a name="firefox-cant-establish-a-connection-to-the-server-at-ltaddressgt-error-in-firebug"></a>"Firefox 无法建立与服务器上的连接&lt;地址&gt;"在 Firebug 中的错误

如果协商的 WebSocket 传输会失败，而是使用另一个传输，可在 Firebug 中出现此错误消息。 此行为是有意安排的。

### <a name="the-remote-certificate-is-invalid-according-to-the-validation-procedure-error-in-net-client-application"></a>在.NET 客户端应用程序中的"远程证书无效，验证过程根据"错误

如果你的服务器要求自定义客户端证书，然后可以将 x509 证书添加到连接之前发出请求。 将证书添加到连接使用`Connection.AddClientCertificate`。

### <a name="connection-drops-after-authentication-times-out"></a>连接中断后身份验证超时

此行为是有意安排的。 当连接处于活动状态; 不能修改身份验证凭据若要刷新的凭据，必须停止并重新启动连接。

### <a name="onconnected-gets-called-twice-when-using-jquery-mobile"></a>OnConnected 被调用两次时使用的 jQuery Mobile

jQuery Mobile`initializePage`函数强制重新执行，每个页面中的脚本，从而创建第二个连接。 此问题的解决方案包括：

- 包括对你的 JavaScript 文件之前的 jQuery Mobile 的引用。
- 禁用`initializePage`函数通过设置`$.mobile.autoInitializePage = false`。
- 等待页后，可以完成初始化之前启动连接。

### <a name="messages-are-delayed-in-silverlight-applications-using-server-sent-events"></a>消息被延迟在 Silverlight 应用程序中使用服务器发送事件

在 Silverlight 中使用服务器发送事件时，消息被延迟。 若要强制长轮询以改为使用，使用以下命令启动连接时：

[!code-css[Main](troubleshooting/samples/sample13.css)]

### <a name="permission-denied-using-forever-frame-protocol"></a>使用"权限被拒绝"永久帧协议

这是一个已知的问题，所述[此处](https://github.com/SignalR/SignalR/issues/1963)。 这种现象，可能会看到使用最新的 JQuery 库;解决方法是降级到 JQuery 1.8.2 应用程序。

<a id="server"></a>

## <a name="compilation-and-server-side-errors"></a>编译和服务器端错误

 以下部分包含对编译器和服务器端运行时错误可能的解决方案。 

### <a name="reference-to-hub-instance-is-null"></a>到中心实例的引用为 null

由于每个连接创建集线器实例时，您不能自己创建实例的中心在代码中。 若要对集线器从中心本身之外调用方法，请参阅[如何调用方法的客户端和管理的中心类外部的组](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub)有关如何获取对的集线器上下文的引用。

### <a name="httpcontextcurrentsession-is-null"></a>HTTPContext.Current.Session 为 null

此行为是有意安排的。 SignalR 不支持 ASP.NET 会话状态，因为启用会话状态将会破坏双工消息传递。

### <a name="no-suitable-method-to-override"></a>没有合适的方法重写

如果使用较旧的文档或博客中的代码，可能会看到此错误。 验证是否未引用的已更改或已弃用的方法名称 (如`OnConnectedAsync`)。

### <a name="hostcontextextensionswebsocketserverurl-is-null"></a>HostContextExtensions.WebSocketServerUrl 为 null

此行为是有意安排的。 此成员已弃用，不应使用。

### <a name="a-route-named-signalrhubs-is-already-in-the-route-collection-error"></a>"名为 signalr.hubs 的路由已路由集合中"错误

将出现此错误，如果`MapHubs`两次由应用程序调用。 某些示例应用程序调用`MapHubs`直接在全局应用程序文件中; 其他人进行调用包装器类中的。 请确保，你的应用程序并未同时。

<a id="vs"></a>

## <a name="visual-studio-issues"></a>Visual Studio 问题

本部分介绍在 Visual Studio 中遇到的问题。

### <a name="script-documents-node-does-not-appear-in-solution-explorer"></a>脚本文档节点不显示在解决方案资源管理器

我们的一些教程进行调试时将你定向到在解决方案资源管理器中的"脚本文档"节点。 此节点由 JavaScript 调试器中，并且将只会显示调试 Internet Explorer 中的浏览器客户端时如果使用 Chrome 或 Firefox，则不会出现该节点。 如果正在运行另一个客户端调试器，例如 Silverlight 调试器 JavaScript 调试器也将不运行。

### <a name="signalr-does-not-work-on-visual-studio-2008-or-earlier"></a>Visual Studio 2008 或更低版本 SignalR 不起作用

此行为是有意安排的。 SignalR 需要.NET Framework 4 或更高版本;这需要在 Visual Studio 2010 或更高版本来开发 SignalR 应用程序。

<a id="iis"></a>

## <a name="iis-issues"></a>IIS 问题

本部分包含使用 Internet Information Services 的问题。

### <a name="web-site-crashes-after-maphubs-call"></a>网站 MapHubs 调用之后就故障

SignalR 的最新版本中已修复此问题。 验证通过更新使用 NuGet 安装使用 SignalR 最新发行的版本。

<a id="azure"></a>

## <a name="azure-issues"></a>Azure 问题

本部分包含使用 Microsoft Azure 的问题。

### <a name="messages-are-not-received-through-the-azure-backplane-after-altering-topic-names"></a>消息不会接收通过 Azure 底板后更改的主题名称

使用 Azure 的基架的主题并不是用户可配置。
