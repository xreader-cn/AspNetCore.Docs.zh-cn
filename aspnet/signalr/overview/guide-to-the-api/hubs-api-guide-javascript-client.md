---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
title: ASP.NET SignalR 中心 API 指南-JavaScript 客户端 |Microsoft Docs
author: bradygaster
description: 本文档介绍了使用 SignalR 版本 2 中 JavaScript 客户端，如浏览器和 Windows 应用商店 (WinJS) applicat 中心 API...
ms.author: bradyg
ms.date: 01/15/2019
ms.assetid: a9fd4dc0-1b96-4443-82ca-932a5b4a8ea4
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
msc.type: authoredcontent
ms.openlocfilehash: 7689fa5b6a3e81c9f767831423eb3efad72e1ab3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836592"
---
<a name="aspnet-signalr-hubs-api-guide---javascript-client"></a>ASP.NET SignalR 中心 API 指南-JavaScript 客户端
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本文档提供使用 SignalR 版本 2 中 JavaScript 客户端，如浏览器和 Windows 应用商店 (WinJS) 应用程序为中心 API 的简介。
>
> SignalR 中心 API，可从连接的客户端到服务器和客户端到服务器进行远程过程调用 (Rpc)。 在服务器代码中，定义可由客户端，调用的方法和调用客户端运行的方法。 在客户端代码中，定义在服务器上，可以调用的方法，并调用在服务器运行的方法。 SignalR 将负责所有为你的客户端-服务器探测功能。
>
> SignalR 还提供了一个称为持久连接的较低级别 API。 SignalR、 集线器和持久性连接的介绍，请参阅[SignalR 简介](../getting-started/introduction-to-signalr.md)。
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

- [生成的代理和它为您的作用](#genproxy)

    - [何时使用生成的代理](#cantusegenproxy)
- [客户端安装程序](#clientsetup)

    - [如何引用动态生成的代理](#dynamicproxy)
    - [如何为 SignalR 创建物理文件生成代理](#manualproxy)
- [如何建立连接](#establishconnection)

    - [$。 connection.hub 是相同对象的 $.hubConnection() 创建](#connequivalence)
    - [异步执行的 start 方法](#asyncstart)
- [如何建立跨域连接](#crossdomain)
- [如何配置连接](#configureconnection)

    - [如何指定查询字符串参数](#querystring)
    - [如何指定传输方法](#transport)
- [如何获取代理的 Hub 类](#getproxy)
- [如何定义服务器可以调用的客户端上的方法](#callclient)
- [如何从客户端调用服务器方法](#callserver)
- [如何处理连接生存期事件](#connectionlifetime)
- [如何处理错误](#handleerrors)
- [如何启用客户端的日志记录](#logging)

有关如何进行编程的服务器或.NET 客户端的文档，请参阅以下资源：

- [SignalR 中心 API 指南-服务器](hubs-api-guide-server.md)
- [SignalR 中心 API 指南-.NET 客户端](hubs-api-guide-net-client.md)

SignalR 2 服务器组件是仅可在.NET 4.5 上 （尽管没有 SignalR 2 上.NET 4.0 的.NET 客户端）。

<a id="genproxy"></a>

## <a name="the-generated-proxy-and-what-it-does-for-you"></a>生成的代理和它为您的作用

您可以编写 JavaScript 客户端与 SignalR 服务使用或不使用 SignalR 将为您生成的代理进行通信。 代理为你的用途是代码的简化的语法用于连接服务器调用，写入方法并在服务器上调用方法。

生成的代理时编写代码来调用服务器方法，使您可以使用看起来像执行本地函数的语法： 您可以编写`serverMethod(arg1, arg2)`而不是`invoke('serverMethod', arg1, arg2)`。 如果您键入了错误的服务器方法名称，生成的代理语法还允许即时和可识别客户端的错误。 如果您手动创建用于定义这些代理的文件，还可以用于编写调用服务器方法的代码获得 IntelliSense 支持。

例如，假设在服务器上有以下 Hub 类：

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample1.cs?highlight=1,3,5)]

下面的代码示例显示用于调用什么如的 JavaScript 代码所示`NewContosoChatMessage`服务器和接收的调用的方法`addContosoChatMessageToPage`从服务器的方法。

**使用生成代理**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample2.js?highlight=1-2,8)]

**不生成代理**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample3.js?highlight=2-3,9)]

<a id="cantusegenproxy"></a>

### <a name="when-to-use-the-generated-proxy"></a>何时使用生成的代理

如果你想要注册的服务器调用的客户端方法的多个事件处理程序，则无法使用生成的代理。 否则为您可以选择使用生成的代理，或不基于您编码的首选项。 如果您选择不使用它，无需引用中的"signalr/中心"URL`script`在客户端代码中的元素。

<a id="clientsetup"></a>

## <a name="client-setup"></a>客户端安装程序

JavaScript 客户端需要对 jQuery 和 SignalR core JavaScript 文件的引用。 JQuery 版本必须是 1.6.4 或主要的更高版本，如 1.7.2、 1.8.2 或 1.9.1。 如果您决定使用生成的代理，则还需要对 SignalR 生成代理的 JavaScript 文件的引用。 下面的示例显示了哪些引用可能如下所示使用生成的代理的 HTML 页面中。

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample4.html)]

必须按以下顺序包含这些引用： jQuery 在此之后，SignalR core SignalR 代理 first、 last。

<a id="dynamicproxy"></a>

### <a name="how-to-reference-the-dynamically-generated-proxy"></a>如何引用动态生成的代理

在前面的示例中，对 SignalR 生成代理的引用是动态生成的 JavaScript 代码，不是指向物理文件。 SignalR 为动态代理创建的 JavaScript 代码，并提供给客户端以响应"/ signalr/中心"URL。 如果指定的 SignalR 连接不同的基 URL 中的服务器上你`MapSignalR`方法中，动态生成的代理文件的 URL 是你使用的自定义 URL"/ 中心"追加到它。

> [!NOTE]
> 对于 Windows 8 （Windows 应用商店） JavaScript 客户端，而不是动态生成一个使用物理代理文件。 有关详细信息，请参阅[How to create for SignalR 的物理文件生成代理](#manualproxy)本主题中更高版本。


在 ASP.NET MVC 4 或 5 Razor 视图中，使用波形符来指代将代理文件引用中的应用程序根目录：

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample5.html)]

有关在 MVC 5 中使用 SignalR 的详细信息，请参阅[SignalR 和 MVC 5 入门](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md)。

在 ASP.NET MVC 3 Razor 视图中，使用`Url.Content`以供代理文件参考：

[!code-cshtml[Main](hubs-api-guide-javascript-client/samples/sample6.cshtml)]

在 ASP.NET Web 窗体应用程序，使用`ResolveClientUrl`代理文件引用或通过使用应用程序根目录相对路径 （开头波形符） ScriptManager 注册：

[!code-aspx[Main](hubs-api-guide-javascript-client/samples/sample7.aspx)]

作为一般规则，用于指定将用于 CSS 或 JavaScript 文件"/ signalr/中心"URL 中使用相同的方法。 如果不使用波形符指定 URL，在某些情况下你的应用程序将正常工作时在 Visual Studio 中使用 IIS Express 测试，但部署到完整 IIS 时将失败并显示 404 错误。 有关详细信息，请参阅**解析对根级资源的引用**中[对于 ASP.NET Web 项目的 Visual Studio 中的 Web 服务器](https://msdn.microsoft.com/library/58wxa9w5.aspx)MSDN 站点上。

当在调试模式下，Visual Studio 2017 中运行 web 项目，如果你使用 Internet Explorer 为您的浏览器，可以看到中的代理文件**解决方案资源管理器**下**脚本**。

若要查看该文件的内容，请双击**中心**。 如果没有使用 Visual Studio 2012 或 2013年和 Internet Explorer 中，或者你不在调试模式下，还可以通过浏览到"/ signalR/中心"URL 来获取文件的内容。 例如，如果您的网站在运行时`http://localhost:56699`，请转到`http://localhost:56699/SignalR/hubs`在浏览器中。

<a id="manualproxy"></a>

### <a name="how-to-create-a-physical-file-for-the-signalr-generated-proxy"></a>如何为 SignalR 创建物理文件生成代理

作为动态生成的代理的替代方法，可以创建具有代理代码的物理文件并引用该文件。 可能想要执行该操作，控制缓存，或者将行为，或若要获取 IntelliSense，在编码时对服务器方法的调用。

若要创建代理文件，请执行以下步骤：

1. 安装[Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) NuGet 包。
2. 打开命令提示符并浏览到*工具*SignalR.exe 文件所在的文件夹。 工具文件夹位于以下位置：

    `[your solution folder]\packages\Microsoft.AspNet.SignalR.Utils.2.1.0\tools`
3. 输入以下命令：

    `signalr ghp /path:[path to the .dll that contains your Hub class]`

    通往你 *.dll*通常*bin*项目文件夹中的文件夹。

    此命令创建名为的文件*server.js*所在的同一文件夹中*signalr.exe*。
4. 将放*server.js*文件在项目中的相应文件夹中，根据应用程序中，将其重命名，并添加对它的引用来代替"signalr/中心"引用。

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a>如何建立连接

您可以建立连接之前，必须创建连接对象，创建代理，并注册事件处理程序可以从服务器调用的方法的。 当安装的代理和事件处理程序，通过调用建立连接`start`方法。

如果使用生成的代理，你无需因为生成的代理代码会为你自己的代码中创建连接对象。

<a id="nogenconnection"></a>

**（与生成的代理） 建立连接**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample8.js?highlight=5)]

**建立连接 （而不生成的代理）**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample9.js?highlight=1,6)]

示例代码使用默认值"/ signalr"连接到 SignalR 服务的 URL。 有关如何指定不同的基 URL 的信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-/signalr URL](hubs-api-guide-server.md#signalrurl)。

默认情况下的中心位置是当前的服务器;如果要连接到另一台服务器，指定的 URL，然后才能调用`start`方法，如以下示例所示：

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample10.js)]

> [!NOTE]
> 通常情况下注册事件处理程序之前调用`start`方法以建立连接。 如果你想要建立连接后注册一些事件处理程序，您可以这样做，但您必须注册之前，调用在事件处理程序中至少一个`start`方法。 原因之一就是在应用程序，可以有多个中心，但您可能不希望触发`OnConnected`如果仅要使用到其中的每个中心上的事件。 建立连接后，在中心的代理服务器上的客户端方法的存在是告知 SignalR 来触发`OnConnected`事件。 如果你未注册任何事件处理程序之前调用`start`方法中，你将能够在中心，但集线器上调用方法`OnConnected`不会调用方法并没有客户端的方法将调用从服务器。


<a id="connequivalence"></a>

### <a name="connectionhub-is-the-same-object-that-hubconnection-creates"></a>$。 connection.hub 是相同对象的 $.hubConnection() 创建

使用生成的代理时，可以看到示例中，从`$.connection.hub`引用的连接对象。 这是通过调用获取的相同对象`$.hubConnection()`时不使用生成的代理。 生成的代理代码创建您的连接通过执行以下语句：

![在生成的代理文件中创建的连接](hubs-api-guide-javascript-client/_static/image3.png)

当您使用生成的代理时，您可以执行任何操作`$.connection.hub`不使用时生成的代理可以执行与连接对象。

<a id="asyncstart"></a>

### <a name="asynchronous-execution-of-the-start-method"></a>异步执行的 start 方法

`start`方法以异步方式执行。 它将返回[jQuery 延迟对象](http://api.jquery.com/category/deferred-object/)，这意味着您可以通过调用方法，如添加回调函数`pipe`， `done`，和`fail`。 如果你想要在建立连接后，执行的代码，如服务器方法调用，该代码放在回调函数中或从回调函数中调用它。 `.done`后已建立的连接，并且任何代码，之后必须执行回调方法在`OnConnected`事件处理程序方法在服务器上的完成执行。

如果将"现已连接"语句从前面的示例作为代码后的下一行`start`方法调用 (不在`.done`回调)，则`console.log`行将按如下所示执行才能建立连接，示例：

![错误的处理方式写入运行建立连接后的代码](hubs-api-guide-javascript-client/_static/image5.png)

<a id="crossdomain"></a>

## <a name="how-to-establish-a-cross-domain-connection"></a>如何建立跨域连接

通常如果浏览器加载来自的页面`http://contoso.com`，在同一个域中，是在的 SignalR 连接`http://contoso.com/signalr`。 如果从页`http://contoso.com`建立到`http://fabrikam.com/signalr`，即跨域连接。 出于安全原因，默认情况下将禁用跨域连接。

在 SignalR 1.x 中的，跨域请求已由单个 EnableCrossDomain 标志控制。 此标志控制 JSONP 和 CORS 请求。 对于更大的灵活性，所有的 CORS 支持已从 SignalR 的服务器组件 （JavaScript 客户端仍使用 CORS 通常如果检测到由浏览器支持它），以及新的 OWIN 中间件变得可用于支持这些方案。

JSONP 需要在客户端 （以支持旧版浏览器中的跨域请求） 上，它将需要通过设置显式启用`EnableJSONP`上`HubConfiguration`对象传递给`true`，如下所示。 JSONP 是默认禁用，因为它比 CORS 不太安全。

**向项目添加 Microsoft.Owin.Cors:** 若要安装此库，请在包管理器控制台中运行以下命令：

`Install-Package Microsoft.Owin.Cors`

此命令会将 2.1.0 添加到你的项目包的版本。

### <a name="calling-usecors"></a>调用 UseCors

 下面的代码段演示如何在 SignalR 2 实现跨域的连接。

**在 SignalR 2 实现跨域请求**

下面的代码演示如何在 SignalR 2 项目中启用 CORS 或 JSONP。 此代码示例使用`Map`并`RunSignalR`而不是`MapSignalR`，以便将 CORS 中间件运行仅针对需要 CORS 支持 SignalR 请求 (而不是对中指定的路径上的所有流量`MapSignalR`。)映射还用于运行针对特定的 URL 前缀，而不是整个应用程序所需的任何其他中间件。

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample11.cs)]

> [!NOTE]
>
> - 未设置`jQuery.support.cors`为 true，在代码中。
>
>     ![未将 jQuery.support.cors 设置为 true](hubs-api-guide-javascript-client/_static/image7.png)
>
>     SignalR 处理 CORS 的使用。 设置`jQuery.support.cors`以 true 禁用 JSONP，因为它会导致 SignalR 假设浏览器支持 CORS。
> - 时要连接到本地主机 URL，Internet Explorer 10 不会考虑它的跨域连接，因此应用程序将在本地处理与 IE 10 即使尚未启用跨域服务器上的连接。
> - 使用的 Internet Explorer 9 的跨域连接的信息，请参阅[此 StackOverflow 线程](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work)。
> - 有关与 Chrome 使用跨域连接的信息，请参阅[此 StackOverflow 线程](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome)。
> - 示例代码使用默认值"/ signalr"连接到 SignalR 服务的 URL。 有关如何指定不同的基 URL 的信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-/signalr URL](hubs-api-guide-server.md#signalrurl)。


<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a>如何配置连接

建立连接之前，您可以指定查询字符串参数或指定的传输方法。

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a>如何指定查询字符串参数

如果你想要将数据发送到服务器，客户端连接时，您可以将查询字符串参数添加到连接对象。 以下示例演示如何在客户端代码中设置的查询字符串参数。

**（使用生成的代理） 调用 start 方法之前设置查询字符串值**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample12.js?highlight=1)]

**在调用 start 方法 （而不生成的代理） 之前设置查询字符串值**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample13.js?highlight=2)]

下面的示例演示如何读取服务器代码中的查询字符串参数。

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample14.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a>如何指定传输方法

作为连接的过程的一部分，SignalR 客户端通常与服务器协商以确定支持的最佳传输服务器和客户端。 如果您已经知道你想要使用哪种的传输，您可以通过在调用时指定的传输方法来绕过这个协商过程`start`方法。

**指定的传输方法 （具有生成的代理） 的客户端代码**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample15.js?highlight=1)]

**指定的传输方法 （而不生成的代理） 的客户端代码**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample16.js?highlight=2)]

或者，可以指定要在其中 SignalR 尝试它们的顺序多个传输方法：

**指定自定义传输的回退方案 （具有生成的代理） 的客户端代码**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample17.js?highlight=1)]

**指定自定义传输的回退方案 （而不生成的代理） 的客户端代码**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample18.js?highlight=2)]

用于指定的传输方法，可以使用以下值：

- "webSockets"
- "foreverFrame"
- "serverSentEvents"
- "longPolling"

以下示例演示如何确定哪种传输方法使用建立的连接。

**显示使用 （与生成的代理） 的连接的传输方法的客户端代码**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample19.js?highlight=2)]

**显示连接 （而不生成的代理） 使用的传输方法的客户端代码**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample20.js?highlight=3)]

有关如何检查服务器代码中的传输方法的信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-如何从上下文属性中获取有关客户端信息](hubs-api-guide-server.md#contextproperty)。 有关传输和回退的详细信息，请参阅[简介 SignalR 的传输和回退](../getting-started/introduction-to-signalr.md#transports)。

<a id="getproxy"></a>

## <a name="how-to-get-a-proxy-for-a-hub-class"></a>如何获取代理的 Hub 类

创建每个连接对象封装有关与包含一个或多个 Hub 类的 SignalR 服务的连接信息。 若要与 Hub 类进行通信，你使用代理对象 （如果不使用的生成的代理），会创建您自己或为您生成。

在客户端上的代理服务器名称是中心类名称的 camel 大小写版本。 SignalR 自动进行此更改，以便 JavaScript 代码可以遵循 JavaScript 约定。

**在服务器上的 hub 类**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample21.cs?highlight=1)]

**获取对生成的客户端代理的引用为中心**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample22.js?highlight=1)]

**创建 Hub 类 （而不生成的代理） 的客户端代理**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample23.cs?highlight=1)]

如果修饰在中心类`HubName`属性，而无需更改情况下使用的确切名称。

**HubName 属性与服务器上的 hub 类**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample24.cs?highlight=1)]

**获取对生成的客户端代理的引用为中心**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample25.js?highlight=1)]

**创建 Hub 类 （而不生成的代理） 的客户端代理**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample26.cs?highlight=1)]

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a>如何定义服务器可以调用的客户端上的方法

若要定义服务器可以从集线器调用的方法，将添加一个事件处理程序到集线器代理通过使用`client`属性生成的代理，或调用`on`方法，如果不使用生成的代理。 参数可以是复杂对象。

添加事件处理程序，然后再调用`start`方法以建立连接。 (如果你想要添加事件处理程序之后调用`start`方法，请参阅中的说明[如何建立连接](#establishconnection)先前在此文档，并使用用于定义一种方法，而无需使用生成的代理所示的语法。)

方法名称匹配不区分大小写。 例如，`Clients.All.addContosoChatMessageToPage`在服务器上将执行`AddContosoChatMessageToPage`， `addContosoChatMessageToPage`，或`addcontosochatmessagetopage`客户端上。

**（具有生成的代理） 的客户端上定义方法**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample27.js?highlight=2)]

**另一种方法 （具有生成的代理） 的客户端上定义方法**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample28.js?highlight=1-2)]

**客户端 （而无需生成的代理，或在调用 start 方法之后添加时） 上定义方法**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample29.js?highlight=3)]

**调用客户端方法的服务器代码**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample30.cs?highlight=5)]

下面的示例包括复杂对象作为方法参数。

**采用 （具有生成的代理） 的复杂对象的客户端上定义方法**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample31.js?highlight=2-3)]

**使用复杂对象 （而不生成的代理） 的客户端上定义方法**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample32.js?highlight=3-4)]

**定义的复杂对象的服务器代码**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample33.cs?highlight=1)]

**使用复杂对象的客户端方法调用的服务器代码**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample34.cs?highlight=3)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a>如何从客户端调用服务器方法

若要从客户端调用服务器方法，使用`server`属性生成的代理或`invoke`中心代理，如果不使用生成的代理上的方法。 返回值或参数可能是复杂对象。

在该中心传入方法名称的 camel 大小写格式版本。 SignalR 自动进行此更改，以便 JavaScript 代码可以遵循 JavaScript 约定。

以下示例演示如何调用服务器方法没有返回值以及如何调用服务器方法没有返回值。

**服务器没有 HubMethodName 属性方法**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample35.cs?highlight=3)]

**在参数中传递的定义的复杂对象的服务器代码**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample36.cs)]

**调用服务器方法 （包含生成的代理） 的客户端代码**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample37.js?highlight=1)]

**客户端代码调用服务器方法 （而不生成的代理）**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample38.js?highlight=1)]

如果修饰中心方法替换`HubMethodName`属性，而无需更改情况下使用该名称。

**服务器方法**HubMethodName 属性

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample39.cs?highlight=3)]

**调用服务器方法 （包含生成的代理） 的客户端代码**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample40.js?highlight=1)]

**客户端代码调用服务器方法 （而不生成的代理）**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample41.js?highlight=1)]

前面的示例说明如何调用没有返回值的服务器方法。 以下示例演示如何调用服务器方法具有返回值。

**服务器代码的方法的返回值**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample42.cs?highlight=3)]

**Stock 类用于**返回值

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample43.cs?highlight=1)]

**调用服务器方法 （包含生成的代理） 的客户端代码**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample44.js?highlight=2,4-5)]

**客户端代码调用服务器方法 （而不生成的代理）**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample45.js?highlight=2,4-5)]

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a>如何处理连接生存期事件

SignalR 提供了以下连接可以处理的生存期事件：

- `starting`：通过连接发送任何数据之前引发。
- `received`：在连接上接收到任何数据时引发。 提供接收到的数据。
- `connectionSlow`：当客户端检测到慢速或频繁删除连接时引发。
- `reconnecting`：基础传输开始重新连接时引发。
- `reconnected`：当基础传输已重新连接时引发。
- `stateChanged`：连接状态更改时引发。 提供的旧状态和新的状态 （连接、 已连接、 正在重新连接或已断开连接）。
- `disconnected`：当连接已断开连接时引发。

例如，如果你想要有可能会导致明显延迟的连接问题时显示警告消息，处理`connectionSlow`事件。

**句柄 （具有生成的代理） 的 connectionSlow 事件**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample46.js?highlight=1)]

**处理 connectionSlow 事件 （而不生成的代理）**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample47.js?highlight=2)]

有关详细信息，请参阅[理解和 SignalR 中的处理连接生存期事件](handling-connection-lifetime-events.md)。

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a>如何处理错误

SignalR JavaScript 客户端提供了`error`可以添加的处理程序的事件。 失败方法还可用于添加处理程序从服务器方法调用导致的错误。

如果未显式启用服务器上的详细的错误消息，SignalR 返回出现错误后的异常对象包含有关错误的最少信息。 例如，如果调用`newContosoChatMessage`失败，错误对象中的错误消息包含"`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`"发送到在生产环境中的客户端的详细的错误消息不建议出于安全原因，但如果你想要启用详细的错误消息在服务器上进行故障排除，使用下面的代码。

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample48.cs?highlight=2)]

下面的示例演示如何添加错误事件的处理程序。

**添加错误处理程序 （具有生成的代理）**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample49.js?highlight=1)]

**添加错误处理程序 （而不生成的代理）**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample50.js?highlight=2)]

下面的示例演示如何处理来自方法调用的错误。

**处理来自方法调用 （与生成的代理） 的错误**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample51.js?highlight=2)]

**处理从方法调用 （而不生成的代理） 错误**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample52.js?highlight=2)]

如果方法调用失败，`error`也会引发事件，因此你的代码中`error`方法处理程序并在`.fail`方法回调会执行。

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a>如何启用客户端的日志记录

若要启用客户端的日志记录的连接上，设置`logging`属性对 connection 对象，然后再调用`start`方法以建立连接。

**（使用生成的代理） 启用日志记录**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample53.js?highlight=1)]

**启用日志记录 （而不生成的代理）**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample54.js?highlight=2)]

若要查看日志，请打开浏览器的开发人员工具，并转到控制台选项卡。本教程介绍的分步说明和屏幕截图显示如何执行此操作，请参阅[服务器与 ASP.NET Signalr-启用日志记录广播](../getting-started/tutorial-server-broadcast-with-signalr.md#enable-logging)。
