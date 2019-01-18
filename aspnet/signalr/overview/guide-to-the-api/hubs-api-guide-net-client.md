---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-net-client
title: ASP.NET SignalR 中心 API 指南-.NET 客户端 (C#) |Microsoft Docs
author: pfletcher
description: 本文档介绍了使用 SignalR.NET 客户端，例如 Windows 应用商店 (WinRT)、 WPF、 Silverlight 和缺点在版本 2 的中心 API...
ms.author: riande
ms.date: 01/15/2019
ms.assetid: 6d02d9f7-94e5-4140-9f51-5a6040f274f6
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-net-client
msc.type: authoredcontent
ms.openlocfilehash: 9981b8b91be3395b1a3aa7e0cabb1b7f455d47be
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396176"
---
<a name="aspnet-signalr-hubs-api-guide---net-client-c"></a>ASP.NET SignalR 中心 API 指南-.NET 客户端 (C#)
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本文档提供使用 SignalR.NET 客户端，例如 Windows 应用商店 (WinRT)、 WPF、 Silverlight 和控制台应用程序中的版本 2 的中心 API 的简介。
>
> SignalR 中心 API，可从连接的客户端到服务器和客户端到服务器进行远程过程调用 (Rpc)。 在服务器代码中，定义可由客户端，调用的方法和调用客户端运行的方法。 在客户端代码中，定义在服务器上，可以调用的方法，并调用在服务器运行的方法。 SignalR 将负责所有为你的客户端-服务器探测功能。
>
> SignalR 还提供了一个称为持久连接的较低级别 API。 简介 SignalR、 集线器和持久性连接，或者显示了如何构建一个完整的 SignalR 应用程序的教程，请参阅[SignalR-Getting Started](../getting-started/index.md)。
>
> ## <a name="software-versions-used-in-this-topic"></a>本主题中使用的软件版本
>
>
> - [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
> - .NET 4.5
> - SignalR 版本 2
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>本主题的早期版本
>
> 有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。
>
> ## <a name="questions-and-comments"></a>问题和提出的意见
>
> 请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。 如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。

## <a name="overview"></a>概述

本文档包含以下各节：

- [客户端安装程序](#clientsetup)
- [如何建立连接](#establishconnection)

    - [Silverlight 客户端的跨域连接](#slcrossdomain)
- [如何配置连接](#configureconnection)

    - [如何在 WPF 客户端中设置的最大并发连接数](#maxconnections)
    - [如何指定查询字符串参数](#querystring)
    - [如何指定传输方法](#transport)
    - [如何指定 HTTP 标头](#httpheaders)
    - [如何指定客户端证书](#clientcertificate)
- [如何创建中心代理](#proxy)
- [如何定义服务器可以调用的客户端上的方法](#callclient)

    - [不带参数的方法](#clientmethodswithoutparms)
    - [具有指定参数类型的参数的方法](#clientmethodswithparmtypes)
    - [使用指定的参数的动态对象的参数的方法](#clientmethodswithdynamparms)
    - [如何删除一个处理程序](#removehandler)
- [如何从客户端调用服务器方法](#callserver)
- [如何处理连接生存期事件](#connectionlifetime)
- [如何处理错误](#handleerrors)
- [如何启用客户端的日志记录](#logging)
- [WPF、 Silverlight 和控制台应用程序的代码可以调用服务器的客户端方法的示例](#wpfsl)

有关示例.NET 客户端项目，请参阅以下资源：

- [gustavo armenta / SignalR 示例](https://github.com/gustavo-armenta/SignalR-Samples)GitHub.com （WinRT，Silverlight 中，控制台应用程序示例） 上。
- [DamianEdwards / SignalR MoveShapeDemo / MoveShape.Desktop](https://github.com/DamianEdwards/SignalR-MoveShapeDemo/tree/master/MoveShape/MoveShape.Desktop) GitHub.com （WPF 示例） 上。
- [SignalR / Microsoft.AspNet.SignalR.Client.Samples](https://github.com/SignalR/SignalR/tree/master/samples/Microsoft.AspNet.SignalR.Client.Samples) GitHub.com （控制台应用程序示例） 上。

有关如何进行编程的服务器或 JavaScript 客户端的文档，请参阅以下资源：

- [SignalR 中心 API 指南-服务器](hubs-api-guide-server.md)
- [SignalR 中心 API 指南-JavaScript 客户端](hubs-api-guide-javascript-client.md)

API 参考主题的链接将指向.NET 4.5 版本的 API。 如果使用的.NET 4，请参阅[API 主题的.NET 4 版本](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx)。

<a id="clientsetup"></a>

## <a name="client-setup"></a>客户端安装程序

安装[Microsoft.AspNet.SignalR.Client](http://nuget.org/packages/Microsoft.AspNet.SignalR.Client) NuGet 包 (不[Microsoft.AspNet.SignalR](http://nuget.org/packages/microsoft.aspnet.signalr)包)。 此程序包为.NET 4 和.NET 4.5 支持 WinRT、 Silverlight、 WPF、 控制台应用程序和 Windows Phone 客户端。

如果在服务器上的版本不同的版本必须在客户端的 SignalR，SignalR 是通常能够适应不同之处。 例如，运行 SignalR 版本 2 的服务器将支持具有 1.1.x 安装的客户端，以及具有版本 2 安装的客户端。 如果在服务器上的版本和客户端上的版本之间的差异太多，或者如果客户端与服务器的较新，SignalR 将引发`InvalidOperationException`客户端将尝试建立连接时出现异常。 错误消息是"`You are using a version of the client that isn't compatible with the server. Client version X.X, server version X.X`"。

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a>如何建立连接

您可以建立连接之前，必须创建`HubConnection`对象，并创建代理。 若要建立连接，请调用`Start`方法`HubConnection`对象。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample1.cs?highlight=1,4)]

> [!NOTE]
> 对于 JavaScript 客户端，需要注册至少一个事件处理程序，然后才能调用`Start`方法以建立连接。 这不是必需的.NET 客户端。 对于 JavaScript 客户端，生成的代理代码会自动创建用于所有存在的集线器代理的服务器上，并注册一个处理程序是如何指示哪个中心为客户端想要使用。 但对于.NET 客户端中心代理手动创建，因此 SignalR 假定，将使用创建的代理的任何中心。


示例代码使用默认值"/ signalr"连接到 SignalR 服务的 URL。 有关如何指定不同的基 URL 的信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-/signalr URL](hubs-api-guide-server.md#signalrurl)。

`Start`方法以异步方式执行。 若要确保后面的代码行不执行之前建立的连接后，请使用`await`在 ASP.NET 4.5 异步方法或`.Wait()`在同步方法。 不要使用`.Wait()`WinRT 客户端中。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample2.cs?highlight=1)]

[!code-css[Main](hubs-api-guide-net-client/samples/sample3.css?highlight=1)]

<a id="slcrossdomain"></a>

### <a name="cross-domain-connections-from-silverlight-clients"></a>Silverlight 客户端的跨域连接

有关如何启用 Silverlight 客户端的跨域连接的信息，请参阅[使服务跨域边界可用](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx)。

<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a>如何配置连接

建立连接之前，您可以指定任何以下选项：

- 并发连接限制。
- 查询字符串参数。
- 传输方法。
- HTTP 标头。
- 客户端证书。

<a id="maxconnections"></a>

### <a name="how-to-set-the-maximum-number-of-concurrent-connections-in-wpf-clients"></a>如何在 WPF 客户端中设置的最大并发连接数

在 WPF 客户端，您可能需要增加最大数量的并发连接从其默认值为 2。 建议的值为 10。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample4.cs?highlight=4)]

有关详细信息，请参阅[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx)。

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a>如何指定查询字符串参数

如果你想要将数据发送到服务器，客户端连接时，您可以将查询字符串参数添加到连接对象。 下面的示例演示如何在客户端代码中设置的查询字符串参数。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample5.cs)]

下面的示例演示如何读取服务器代码中的查询字符串参数。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample6.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a>如何指定传输方法

作为连接的过程的一部分，SignalR 客户端通常与服务器协商以确定支持的最佳传输服务器和客户端。 如果您已经知道你想要使用哪种的传输，可以绕过这个协商过程。 若要指定的传输方法，在传入的传输对象的 Start 方法。 下面的示例演示如何在客户端代码中指定的传输方法。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample7.cs?highlight=4)]

[Microsoft.AspNet.SignalR.Client.Transports](https://msdn.microsoft.com/library/jj918090(v=vs.111).aspx)命名空间包括可用于指定传输的以下类。

- [LongPollingTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.longpollingtransport(v=vs.111).aspx)
- [ServerSentEventsTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.serversenteventstransport(v=vs.111).aspx)
- [WebSocketTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.websockettransport(v=vs.111).aspx) （可用仅在服务器和客户端使用.NET 4.5 时。）
- [AutoTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.autotransport(v=vs.111).aspx) （自动选择最佳传输支持的客户端和服务器。 这是默认的传输。 通过这个到`Start`方法具有相同的效果不传递任何内容。)

此列表中不包括 ForeverFrame 传输，因为仅由浏览器使用。

有关如何检查服务器代码中的传输方法的信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-如何从上下文属性中获取有关客户端信息](hubs-api-guide-server.md#contextproperty)。 有关传输和回退的详细信息，请参阅[简介 SignalR 的传输和回退](../getting-started/introduction-to-signalr.md#transports)。

<a id="httpheaders"></a>

### <a name="how-to-specify-http-headers"></a>如何指定 HTTP 标头

若要设置 HTTP 标头，使用`Headers`连接对象上的属性。 下面的示例演示如何添加 HTTP 标头。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample8.cs?highlight=2)]

<a id="clientcertificate"></a>

### <a name="how-to-specify-client-certificates"></a>如何指定客户端证书

若要添加客户端证书，请使用`AddClientCertificate`连接对象上的方法。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample9.cs?highlight=2)]

<a id="proxy"></a>

## <a name="how-to-create-the-hub-proxy"></a>如何创建中心代理

为了在服务器上，可以调用集线器的客户端上定义的方法并调用在服务器上的集线器的方法，通过调用创建集线器的代理`CreateHubProxy`连接对象上。 在字符串中传递到`CreateHubProxy`是你 Hub 类的名称或指定的名称`HubName`属性如果在服务器上使用一种。 名称匹配不区分大小写。

**在服务器上的 hub 类**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample10.cs?highlight=1)]

**创建 Hub 类用于客户端代理**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample11.cs?highlight=2)]

如果修饰在中心类`HubName`属性，请使用该名称。

**在服务器上的 hub 类**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample12.cs)]

**创建 Hub 类用于客户端代理**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample13.cs?highlight=2)]

如果您调用`HubConnection.CreateHubProxy`多次使用相同`hubName`，获取相同的缓存`IHubProxy`对象。

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a>如何定义服务器可以调用的客户端上的方法

若要定义服务器可以调用的方法，请使用代理服务器的`On`方法来注册事件处理程序。

方法名称匹配不区分大小写。 例如，`Clients.All.UpdateStockPrice`在服务器上将执行`updateStockPrice`， `updatestockprice`，或`UpdateStockPrice`客户端上。

不同的客户端平台具有不同的要求如何编写方法代码来更新 UI。 所示的示例适用于 WinRT (Windows 应用商店.NET) 客户端。 中提供 WPF、 Silverlight 和控制台应用程序示例[本主题后面的单独部分](#wpfsl)。

<a id="clientmethodswithoutparms"></a>

### <a name="methods-without-parameters"></a>不带参数的方法

如果正在处理的方法没有参数，使用的非泛型重载`On`方法：

**服务器代码调用不带参数的客户端方法**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample14.cs?highlight=5)]

**从服务器不带参数调用方法的 WinRT 客户端代码 ([请参阅本主题中的更高版本的 WPF 和 Silverlight 示例](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample15.cs)]

<a id="clientmethodswithparmtypes"></a>

### <a name="methods-with-parameters-specifying-the-parameter-types"></a>具有指定参数类型的参数的方法

如果您正在处理该方法具有参数，指定的参数类型为泛型类型的`On`方法。 有的泛型重载`On`方法，以使您可以指定最多 8 个参数 (在 Windows Phone 7 上 4)。 在以下示例中，一个参数发送到`UpdateStockPrice`方法。

**服务器代码调用带有参数的客户端方法**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample16.cs?highlight=3)]

**使用参数的 Stock 类**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample17.cs)]

**WinRT 客户端代码的方法调用从服务器使用的参数 ([请参阅本主题中的更高版本的 WPF 和 Silverlight 示例](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample18.cs?highlight=1,5)]

<a id="clientmethodswithdynamparms"></a>

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a>使用指定的参数的动态对象的参数的方法

作为泛型类型的指定参数的替代方法作为`On`方法，您可以指定参数作为动态对象：

**服务器代码调用带有参数的客户端方法**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample19.cs?highlight=3)]

**使用参数的 Stock 类**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample20.cs)]

**WinRT 客户端代码的方法调用从 server 参数，并为该参数使用一个动态对象 ([请参阅本主题中的更高版本的 WPF 和 Silverlight 示例](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample21.cs?highlight=1,5)]

<a id="removehandler"></a>

### <a name="how-to-remove-a-handler"></a>如何删除一个处理程序

若要删除一个处理程序，请调用其`Dispose`方法。

**从服务器调用的方法的客户端代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample22.cs?highlight=1)]

**若要删除的处理程序的客户端代码**

[!code-css[Main](hubs-api-guide-net-client/samples/sample23.css?highlight=1)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a>如何从客户端调用服务器方法

若要在服务器上调用方法，使用`Invoke`中心代理上的方法。

如果服务器方法没有返回值，请使用非泛型重载`Invoke`方法。

**服务器没有返回值的方法的代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample24.cs?highlight=3)]

**客户端代码调用没有返回值的方法**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample25.cs?highlight=1)]

如果服务器方法返回值，指定的返回类型的泛型类型作为`Invoke`方法。

**服务器代码的方法的返回值，采用复杂类型参数**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample26.cs?highlight=1)]

**使用参数和返回值的 Stock 类**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample27.cs)]

**客户端代码调用方法的返回值和 ASP.NET 4.5 的异步方法中采用复杂类型参数，**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample28.cs?highlight=1-2)]

**客户端代码调用方法的返回值和复杂类型参数，采用一种同步方法**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample29.cs?highlight=1-2)]

`Invoke`方法以异步方式执行，并返回`Task`对象。 如果未指定`await`或`.Wait()`，调用该方法执行完毕之前，将执行下的一行代码。

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a>如何处理连接生存期事件

SignalR 提供了以下连接可以处理的生存期事件：

- `Received`：在连接上接收到任何数据时引发。 提供接收到的数据。
- `ConnectionSlow`：当客户端检测到慢速或频繁删除连接时引发。
- `Reconnecting`：基础传输开始重新连接时引发。
- `Reconnected`：当基础传输已重新连接时引发。
- `StateChanged`：连接状态更改时引发。 提供的旧状态和新的状态。 有关连接状态的值请参阅[ConnectionState 枚举](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connectionstate(v=vs.111).aspx)。
- `Closed`：当连接已断开连接时引发。

例如，如果你想要显示的错误的不严重，但会导致间歇性连接问题的警告消息，如缓慢或频繁删除的连接，处理`ConnectionSlow`事件。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample30.cs)]

有关详细信息，请参阅[理解和 SignalR 中的处理连接生存期事件](handling-connection-lifetime-events.md)。

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a>如何处理错误

如果未显式启用服务器上的详细的错误消息，SignalR 返回出现错误后的异常对象包含有关错误的最少信息。 例如，如果调用`newContosoChatMessage`失败，错误对象中的错误消息包含"`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`"发送到在生产环境中的客户端的详细的错误消息不建议出于安全原因，但如果你想要启用详细的错误消息在服务器上进行故障排除，使用下面的代码。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample31.cs?highlight=2)]

<a id="handleerrors"></a>

若要处理 SignalR 引发的错误，可以添加的处理程序`Error`连接对象上的事件。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample32.cs)]

若要处理的方法调用中的错误，请将代码包装在 try catch 块中。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample33.cs)]

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a>如何启用客户端的日志记录

若要启用客户端日志记录，请设置`TraceLevel`和`TraceWriter`连接对象上的属性。

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample34.cs?highlight=2-3)]

<a id="wpfsl"></a>

## <a name="wpf-silverlight-and-console-application-code-samples-for-client-methods-that-the-server-can-call"></a>WPF、 Silverlight 和控制台应用程序的代码可以调用服务器的客户端方法的示例

用于定义服务器可以调用的客户端方法的前面所示的代码示例适用于 WinRT 客户端。 下面的示例演示的 WPF、 Silverlight 和控制台应用程序客户端的等效代码。

### <a name="methods-without-parameters"></a>不带参数的方法

**从服务器不带参数调用方法的 WPF 客户端代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample35.cs?highlight=1)]

**从服务器不带参数调用方法的 Silverlight 客户端代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample36.cs?highlight=1)]

**从服务器不带参数调用方法的控制台应用程序客户端代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample37.cs?highlight=1)]

### <a name="methods-with-parameters-specifying-the-parameter-types"></a>具有指定参数类型的参数的方法

**从服务器使用的参数调用方法的 WPF 客户端代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample38.cs?highlight=1,4)]

**从服务器使用的参数调用方法的 Silverlight 客户端代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample39.cs?highlight=1,5)]

**从服务器使用的参数调用方法的控制台应用程序客户端代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample40.cs?highlight=1-2)]

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a>使用指定的参数的动态对象的参数的方法

**从服务器使用的参数，使用动态对象作为参数调用方法的 WPF 客户端代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample41.cs?highlight=1,4)]

**从服务器使用的参数，使用动态对象作为参数调用方法的 Silverlight 客户端代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample42.cs?highlight=1,5)]

**从服务器使用的参数，使用动态对象作为参数调用方法的控制台应用程序客户端代码**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample43.cs?highlight=1-2)]
