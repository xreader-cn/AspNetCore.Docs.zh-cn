---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-server
title: ASP.NET SignalR 中心 API 指南-服务器 (C#) |Microsoft Docs
author: bradygaster
description: 本文档介绍了 SignalR 版本 2，ASP.NET SignalR 中心 API 的服务器端编程的代码示例演示...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: b19913e5-cd8a-4e4b-a872-5ac7a858a934
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-server
msc.type: authoredcontent
ms.openlocfilehash: a28663c8d5c679f85e863e7d0b4523a6f4dd4a1f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837450"
---
<a name="aspnet-signalr-hubs-api-guide---server-c"></a>ASP.NET SignalR 中心 API 指南-服务器 (C#)
====================
通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom Dykstra](https://github.com/tdykstra)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本文档提供了简介 SignalR 版本 2，ASP.NET SignalR 中心 API 的服务器端编程的代码示例演示常见的选项。
> 
> SignalR 中心 API，可从连接的客户端到服务器和客户端到服务器进行远程过程调用 (Rpc)。 在服务器代码中，定义可由客户端，调用的方法和调用客户端运行的方法。 在客户端代码中，定义在服务器上，可以调用的方法，并调用在服务器运行的方法。 SignalR 将负责所有为你的客户端-服务器探测功能。
> 
> SignalR 还提供了一个称为持久连接的较低级别 API。 SignalR、 集线器和持久性连接的介绍，请参阅[SignalR 2 简介](../getting-started/introduction-to-signalr.md)。
> 
> ## <a name="software-versions-used-in-this-topic"></a>本主题中使用的软件版本
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - .NET 4.5
> - SignalR 版本 2
>   
> 
> 
> ## <a name="topic-versions"></a>主题版本
> 
> 有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。
> 
> ## <a name="questions-and-comments"></a>问题和提出的意见
> 
> 请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。 如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。

## <a name="overview"></a>概述

本文档包含以下各节：

- [如何注册 SignalR 中间件](#route)

    - [/Signalr URL](#signalrurl)
    - [配置 SignalR 选项](#options)
- [如何创建和使用 Hub 类](#hubclass)

    - [中心对象生存期](#transience)
    - [Camel 大小写的 JavaScript 客户端中的中心名称](#hubnames)
    - [多个中心](#multiplehubs)
    - [强类型化中心](#stronglytypedhubs)
- [如何在客户端可以调用的中心类中定义的方法](#hubmethods)

    - [在 JavaScript 客户端中的方法名称的 camel 大小写](#methodnames)
    - [当以异步方式执行](#asyncmethods)
    - [定义重载](#overloads)
    - [报告从集线器方法调用的进度](#progress)
- [如何从 Hub 类方法调用客户端](#callfromhub)

    - [选择哪些客户端将收到 RPC](#selectingclients)
    - [方法名称没有编译时验证](#dynamicmethodnames)
    - [不区分大小写的方法名称匹配](#caseinsensitive)
    - [异步执行](#asyncclient)
- [如何从 Hub 类管理组成员身份](#groupsfromhub)

    - [异步执行 Add 和 Remove 方法](#asyncgroupmethods)
    - [组成员身份暂留](#grouppersistence)
    - [单用户组](#singleusergroups)
- [如何处理中心类中的连接生存期事件](#connectionlifetime)

    - [OnConnected、 OnDisconnected 和 OnReconnected 调用时](#onreconnected)
    - [调用方状态不会填充](#nocallerstate)
- [如何从上下文属性中获取有关客户端的信息](#contextproperty)
- [如何将客户端和 Hub 类之间传递状态](#passstate)
- [如何处理错误，Hub 类](#handleErrors)
- [如何调用方法的客户端和管理的中心类外部的组](#callfromoutsidehub)

    - [调用客户端方法](#callingclientsoutsidehub)
    - [管理组成员资格](#managinggroupsoutsidehub)
- [如何启用跟踪](#tracing)
- [如何自定义中心管道](#hubpipeline)

有关如何将客户端程序的文档，请参阅以下资源：

- [SignalR 中心 API 指南-JavaScript 客户端](hubs-api-guide-javascript-client.md)
- [SignalR 中心 API 指南-.NET 客户端](hubs-api-guide-net-client.md)

仅在.NET 4.5 中提供了用于 SignalR 2 服务器组件。 运行.NET 4.0 的服务器必须使用 SignalR 1.x 版。

<a id="route"></a>

## <a name="how-to-register-signalr-middleware"></a>如何注册 SignalR 中间件

若要定义的路由的客户端将用于连接到中心，请调用`MapSignalR`方法时在应用程序启动。 `MapSignalR` 是[扩展方法](https://msdn.microsoft.com/library/vstudio/bb383977.aspx)为`OwinExtensions`类。 下面的示例演示如何定义使用的 OWIN 启动类的 SignalR 集线器路由。

[!code-csharp[Main](hubs-api-guide-server/samples/sample1.cs)]

如果要添加到 ASP.NET MVC 应用程序的 SignalR 功能，请确保 SignalR 路由在其他路由之前添加。 有关详细信息，请参阅[教程：使用 SignalR 2 和 MVC 5 入门](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md)。

<a id="signalrurl"></a>

### <a name="the-signalr-url"></a>/Signalr URL

默认情况下，客户端将用于连接到中心的路由 URL 是"/ signalr"。 （不要将此 URL 使用"/ signalr/中心"URL，这是自动生成的 JavaScript 文件混淆。 有关生成的代理的详细信息，请参阅[SignalR 中心 API 指南-JavaScript 客户端-生成的代理和它为您完成](hubs-api-guide-javascript-client.md#genproxy)。)

可能有特殊的情况下，它让 SignalR; 无法使用此基 URL例如，名为在项目中有一个文件夹*signalr*并且不想要更改的名称。 在这种情况下，更改基 URL，如以下示例所示 (替换为"/ signalr"在与你所需的 URL 的示例代码)。

**指定的 URL 的服务器代码**

[!code-csharp[Main](hubs-api-guide-server/samples/sample2.cs?highlight=1)]

**指定的 URL （具有生成的代理） 的 JavaScript 客户端代码**

[!code-javascript[Main](hubs-api-guide-server/samples/sample3.js?highlight=1)]

**指定 （而不生成的代理） 的 URL 的 JavaScript 客户端代码**

[!code-javascript[Main](hubs-api-guide-server/samples/sample4.js?highlight=1)]

**.NET 客户端代码，它指定的 URL**

[!code-csharp[Main](hubs-api-guide-server/samples/sample5.cs?highlight=1)]

<a id="options"></a>

### <a name="configuring-signalr-options"></a>配置 SignalR 选项

重载的`MapSignalR`方法使您能够指定自定义 URL、 自定义依赖关系解析程序，以及以下选项：

- 启用从浏览器客户端使用 CORS 或 JSONP 的跨域调用。

    通常如果浏览器加载来自的页面`http://contoso.com`，在同一个域中，是在的 SignalR 连接`http://contoso.com/signalr`。 如果从页`http://contoso.com`建立到`http://fabrikam.com/signalr`，即跨域连接。 出于安全原因，默认情况下将禁用跨域连接。 有关详细信息，请参阅[ASP.NET SignalR 中心 API 指南-JavaScript 客户端-如何建立跨域连接](hubs-api-guide-javascript-client.md#crossdomain)。
- 启用详细的错误消息。

    发生错误时，SignalR 的默认行为是将一条没有有关发生了什么情况的详细信息的通知消息发送到客户端。 向客户端发送详细的错误消息建议不要在生产中，因为恶意用户可能能够使用攻击针对应用程序中的信息。 有关故障排除，可以使用此选项以暂时启用更详细的错误报告。
- 禁用自动生成的 JavaScript 代理文件。

    默认情况下，与代理服务器的 JavaScript 文件的中心类生成响应的 URL"/ signalr/中心"。 如果不想要使用的 JavaScript 代理，或如果你想要手动生成此文件，并引用你的客户端中的物理文件，可以使用此选项以禁用代理生成。 有关详细信息，请参阅[SignalR 中心 API 指南-JavaScript 客户端-How to create for SignalR 的物理文件生成代理](hubs-api-guide-javascript-client.md#manualproxy)。

下面的示例演示如何在调用中指定的 SignalR 连接 URL 和这些选项`MapSignalR`方法。 若要指定自定义 URL，将为"/ signalr"在你想要使用的 URL 的示例中。

[!code-csharp[Main](hubs-api-guide-server/samples/sample6.cs)]

<a id="hubclass"></a>

## <a name="how-to-create-and-use-hub-classes"></a>如何创建和使用 Hub 类

若要创建一个中心，请创建派生的类[Microsoft.Aspnet.Signalr.Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx)。 下面的示例演示一个简单的中心类的聊天应用程序。

[!code-csharp[Main](hubs-api-guide-server/samples/sample7.cs)]

在此示例中，连接的客户端可以调用`NewContosoChatMessage`方法，并接收的数据时，将广播到所有连接的客户端。

<a id="transience"></a>

### <a name="hub-object-lifetime"></a>中心对象生存期

不实例化 Hub 类或从服务器; 在代码中调用其方法所有的可为您通过 SignalR 集线器管道。 SignalR 在每次需要处理一个中心操作，例如当客户端连接、 断开连接，或对服务器发出方法调用创建 Hub 类的新实例。

Hub 类的实例是暂时的因为不能使用它们来保持到下一个方法调用中的状态。 每次服务器接收方法调用从客户端，您的中心类流程的新实例的消息。 若要保留通过多个连接和方法调用的状态，使用如一个数据库或静态变量的某些其他方法，Hub 类或不是派生的不同类上`Hub`。 如果您将保留在内存中的数据，Hub 类上使用静态变量之类的方法的数据将丢失时应用域回收数。

如果你想要从你自己的中心类外运行的代码将消息发送到客户端，则不能通过实例化的中心类实例，但你可以执行此操作通过为您的中心类获取对 SignalR 上下文对象的引用。 有关详细信息，请参阅[如何调用方法的客户端和管理的中心类外部的组](#callfromoutsidehub)本主题中更高版本。

<a id="hubnames"></a>

### <a name="camel-casing-of-hub-names-in-javascript-clients"></a>Camel 大小写的 JavaScript 客户端中的中心名称

默认情况下，JavaScript 客户端通过使用 camel 大小写版本的类名称引用中心。 SignalR 自动进行此更改，以便 JavaScript 代码可以遵循 JavaScript 约定。 前面的示例将称为`contosoChatHub`JavaScript 代码中。

**服务器**

[!code-csharp[Main](hubs-api-guide-server/samples/sample8.cs?highlight=1)]

**使用生成的代理的 JavaScript 客户端**

[!code-javascript[Main](hubs-api-guide-server/samples/sample9.js?highlight=1)]

如果你想要指定其他名称的客户端要使用，请添加`HubName`属性。 当你使用`HubName`属性，没有任何名称更改为 JavaScript 客户端上混合大小写。

**服务器**

[!code-csharp[Main](hubs-api-guide-server/samples/sample10.cs?highlight=1)]

**使用生成的代理的 JavaScript 客户端**

[!code-javascript[Main](hubs-api-guide-server/samples/sample11.js?highlight=1)]

<a id="multiplehubs"></a>

### <a name="multiple-hubs"></a>多个中心

应用程序中，可以定义多个中心类。 当这样做时，共享此连接，但是独立的组：

- 所有客户端将使用相同的 URL 来建立与你的服务的 SignalR 连接 ("/ signalr"或你的自定义 URL，如果你指定一个)，由服务定义和所有中心使用连接。

    与单个类中定义所有中心功能相比的多个中心没有性能差异。
- 所有中心都获取相同的 HTTP 请求信息。

    由于所有中心都共享相同的连接，服务器获取的唯一 HTTP 请求信息是什么传入建立 SignalR 连接的原始 HTTP 请求。 如果使用连接请求将信息从客户端传递到服务器，通过指定查询字符串，不能向不同的中心提供不同的查询字符串。 所有中心将都接收相同的信息。
- 生成的 JavaScript 代理文件将包含用于在一个文件中的所有集线器代理。

    有关 JavaScript 代理的信息，请参阅[SignalR 中心 API 指南-JavaScript 客户端-生成的代理和它为您完成](hubs-api-guide-javascript-client.md#genproxy)。
- 在中心内定义组。

    在 SignalR 可以定义名为组，以将广播到连接的客户端的子集。 组单独为每个中心维护。 例如，一个名为"管理员"组将包括一组的客户端应用`ContosoChatHub`类和相同的组名称所指一组不同的客户端在`StockTickerHub`类。

<a id="stronglytypedhubs"></a>
### <a name="strongly-typed-hubs"></a>强类型化中心

若要定义一个接口，使您的客户端可以将集线器方法引用 （和集线器方法上的启用 Intellisense），派生从中心`Hub<T>`（在 SignalR 2.1 中引入） 而非`Hub`:

[!code-csharp[Main](hubs-api-guide-server/samples/sample12.cs)]

<a id="hubmethods"></a>

## <a name="how-to-define-methods-in-the-hub-class-that-clients-can-call"></a>如何在客户端可以调用的中心类中定义的方法

若要公开你想要从客户端调用集线器上的方法，声明一个公共方法，如以下示例所示。

[!code-csharp[Main](hubs-api-guide-server/samples/sample13.cs?highlight=3)]

[!code-csharp[Main](hubs-api-guide-server/samples/sample14.cs?highlight=3)]

您可以指定返回类型和参数，包括复杂类型和数组，就像在任何 C# 方法中。 客户端和服务器之间进行通信的接收中的参数或返回到调用方的任何数据是通过使用 JSON，和 SignalR 会自动处理复杂对象的绑定和对象的数组。

<a id="methodnames"></a>

### <a name="camel-casing-of-method-names-in-javascript-clients"></a>在 JavaScript 客户端中的方法名称的 camel 大小写

默认情况下，JavaScript 客户端通过使用 camel 大小写版本的方法名称引用中心的方法。 SignalR 自动进行此更改，以便 JavaScript 代码可以遵循 JavaScript 约定。

**服务器**

[!code-csharp[Main](hubs-api-guide-server/samples/sample15.cs?highlight=1)]

**使用生成的代理的 JavaScript 客户端**

[!code-javascript[Main](hubs-api-guide-server/samples/sample16.js?highlight=1)]

如果你想要指定其他名称的客户端要使用，请添加`HubMethodName`属性。

**服务器**

[!code-csharp[Main](hubs-api-guide-server/samples/sample17.cs?highlight=1)]

**使用生成的代理的 JavaScript 客户端**

[!code-javascript[Main](hubs-api-guide-server/samples/sample18.js?highlight=1)]

<a id="asyncmethods"></a>

### <a name="when-to-execute-asynchronously"></a>当以异步方式执行

如果该方法将是长时间运行或已进行的工作，将涉及等待，如数据库查找或 web 服务调用，使集线器方法通过返回异步[任务](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx)(来代替`void`返回) 或[任务&lt;T&gt; ](https://msdn.microsoft.com/library/dd321424.aspx)对象 (来代替`T`返回类型)。 当您返回`Task`对象中的方法，SignalR 会等待`Task`若要完成，并将发送未包装的结果返回给客户端，以便在客户端中的方法调用的代码没有差别。

使集线器方法异步可避免阻止连接时使用 WebSocket 传输。 当集线器方法以同步方式执行，并传输协议是 WebSocket 时，中心方法完成之前，将阻止从同一个客户端集线器方法的后续调用。

下面的示例演示的相同方法编码同步运行，或以异步方式后, 跟适用于调用任一版本的 JavaScript 客户端代码。

**同步**

[!code-csharp[Main](hubs-api-guide-server/samples/sample19.cs)]

**异步**

[!code-csharp[Main](hubs-api-guide-server/samples/sample20.cs?highlight=1,7-8)]

**使用生成的代理的 JavaScript 客户端**

[!code-javascript[Main](hubs-api-guide-server/samples/sample21.js)]

有关如何在 ASP.NET 4.5 中使用异步方法的详细信息，请参阅[ASP.NET MVC 4 中使用异步方法](../../../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md)。

<a id="overloads"></a>

### <a name="defining-overloads"></a>定义重载

如果你想要定义一种方法的重载，每个重载中的参数数量必须不同。 如果只是通过指定不同的参数类型区分重载方法，Hub 类会编译但 SignalR 服务将在引发异常时，客户端尝试运行时调用的重载之一。

<a id="progress"></a>
### <a name="reporting-progress-from-hub-method-invocations"></a>报告从集线器方法调用的进度

SignalR 2.1 添加了对支持[进度报告模式](https://blogs.msdn.com/b/dotnet/archive/2012/06/06/async-in-4-5-enabling-progress-and-cancellation-in-async-apis.aspx)在.NET 4.5 中引入的。 若要实现进度报告，定义`IProgress<T>`可以访问你的客户端在集线器方法的参数：

[!code-csharp[Main](hubs-api-guide-server/samples/sample22.cs)]

在编写长时间运行的服务器方法时，务必使用异步编程模式等 Async / Await 而不是阻塞中心线程。

<a id="callfromhub"></a>

## <a name="how-to-call-client-methods-from-the-hub-class"></a>如何从 Hub 类方法调用客户端

若要从服务器调用客户端方法，请使用`Clients`Hub 类中的方法中的属性。 下面的示例演示调用的服务器代码`addNewMessageToPage`上所有连接的客户端，并在 JavaScript 客户端中定义的方法的客户端代码。

**服务器**

[!code-csharp[Main](hubs-api-guide-server/samples/sample23.cs?highlight=5)]

调用客户端方法是一个异步操作并返回`Task`。 使用`await`:

* 若要确保将消息发送不会出错。 
* 若要启用捕获和处理 try catch 块中的错误。

**使用生成的代理的 JavaScript 客户端**

[!code-html[Main](hubs-api-guide-server/samples/sample24.html?highlight=1)]

无法从客户端方法; 获取返回值语法如下`int x = Clients.All.add(1,1)`不起作用。

您可以指定复杂类型和数组作为参数。 下面的示例将复杂类型传递给方法参数中的客户端。

**调用使用复杂对象的客户端方法的服务器代码**

[!code-csharp[Main](hubs-api-guide-server/samples/sample25.cs?highlight=3)]

**定义的复杂对象的服务器代码**

[!code-csharp[Main](hubs-api-guide-server/samples/sample26.cs?highlight=1)]

**使用生成的代理的 JavaScript 客户端**

[!code-javascript[Main](hubs-api-guide-server/samples/sample27.js?highlight=2-3)]

<a id="selectingclients"></a>

### <a name="selecting-which-clients-will-receive-the-rpc"></a>选择哪些客户端将收到 RPC

客户端属性返回[HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx)提供若干选项用于指定哪些客户端将接收 RPC 的对象：

- 所有连接的客户端。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample28.cs)]
- 仅调用客户端。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample29.cs)]
- 除调用客户端之外的所有客户端。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample30.cs)]
- 特定的客户端标识的连接 id。

    [!code-css[Main](hubs-api-guide-server/samples/sample31.css)]

    此示例调用`addContosoChatMessageToPage`上调用客户端并且具有与使用相同的效果`Clients.Caller`。
- 所有连接的客户端除外指定客户端，由连接 ID 标识。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample32.cs)]
- 指定组中的所有连接的客户端。

    [!code-css[Main](hubs-api-guide-server/samples/sample33.css)]
- 指定组中的所有连接的客户端除外指定客户端，由连接 ID 标识。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample34.cs)]
- 所有连接的客户端指定组中除调用客户端。

    [!code-css[Main](hubs-api-guide-server/samples/sample35.css)]
- 用户 Id 标识的特定用户。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample36.cs)]

    默认情况下，这是`IPrincipal.Identity.Name`，但这可以通过更改[IUserIdProvider 实现注册到全局主机](mapping-users-to-connections.md#IUserIdProvider)。
- 所有客户端和组列表中的连接 Id。

    [!code-css[Main](hubs-api-guide-server/samples/sample37.css)]
- 组的列表。

    [!code-css[Main](hubs-api-guide-server/samples/sample38.css)]
- 按名称的用户。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample39.cs)]
- （在 SignalR 2.1 中引入） 的用户名称的列表。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample40.cs)]

<a id="dynamicmethodnames"></a>

### <a name="no-compile-time-validation-for-method-names"></a>方法名称没有编译时验证

您指定的方法名称被解释为动态对象，这意味着没有 IntelliSense 或它的编译时验证。 在运行时计算表达式。 方法调用执行时，SignalR 将方法名称和参数值发送到客户端，并在客户端有一种方法名称相匹配，调用方法和参数值传递到它。 如果在客户端上不找到任何匹配的方法，则不会引发错误。 SignalR 将传输到后台客户端时调用的客户端方法的数据格式的信息，请参阅[SignalR 简介](../getting-started/introduction-to-signalr.md)。

<a id="caseinsensitive"></a>

### <a name="case-insensitive-method-name-matching"></a>不区分大小写的方法名称匹配

方法名称匹配不区分大小写。 例如，`Clients.All.addContosoChatMessageToPage`在服务器上将执行`AddContosoChatMessageToPage`， `addcontosochatmessagetopage`，或`addContosoChatMessageToPage`客户端上。

<a id="asyncclient"></a>

### <a name="asynchronous-execution"></a>异步执行

调用该方法以异步方式执行。 对客户端的方法调用将立即执行而无需等待 SignalR 完成传输到客户端的数据，除非您指定后面的代码行应等待方法完成后的任何代码。 下面的代码示例演示如何按顺序执行两个客户端方法。

**使用 Await (.NET 4.5)**

[!code-csharp[Main](hubs-api-guide-server/samples/sample41.cs?highlight=1,3)]

如果使用`await`等待，直到下的一行代码执行之前完成了客户端方法，这并不表示客户端在下的一行代码执行之前，将实际接收该消息。 "完成"的客户端方法调用仅意味着，SignalR 已完成发送消息所需的所有内容。 如果需要验证客户端收到消息，您必须自己进行编程的机制。 例如，您可以编写代码`MessageReceived`方法的中心，并在`addContosoChatMessageToPage`方法在客户端可以调用`MessageReceived`执行操作之后任何起作用，您需要在客户端上执行操作。 在`MessageReceived`在中心可以执行任何工作取决于实际的客户端接收和处理的原始方法调用。

### <a name="how-to-use-a-string-variable-as-the-method-name"></a>如何使用字符串变量作为方法名称

如果你想要调用的客户端方法通过使用字符串变量作为方法名称，将强制转换`Clients.All`(或`Clients.Others`，`Clients.Caller`等) 对`IClientProxy`，然后调用[Invoke （methodName，args...）](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.iclientproxy.invoke(v=vs.111).aspx).

[!code-csharp[Main](hubs-api-guide-server/samples/sample42.cs)]

<a id="groupsfromhub"></a>

## <a name="how-to-manage-group-membership-from-the-hub-class"></a>如何从 Hub 类管理组成员身份

SignalR 中的组提供一种方法将消息广播到连接的客户端的指定子集。 一个组可以具有任意数量的客户端，并在客户端可以是任意数量的组的成员。

若要管理组成员身份，使用[添加](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx)并[删除](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx)提供的方法`Groups`Hub 类的属性。 下面的示例演示`Groups.Add`和`Groups.Remove`集线器方法调用的客户端代码中使用的方法后跟调用它们的 JavaScript 客户端代码。

**服务器**

[!code-csharp[Main](hubs-api-guide-server/samples/sample43.cs?highlight=5,10)]

**使用生成的代理的 JavaScript 客户端**

[!code-javascript[Main](hubs-api-guide-server/samples/sample44.js)]

[!code-javascript[Main](hubs-api-guide-server/samples/sample45.js)]

您无需显式创建组。 一组自动创建第一次调用中指定其名称的有效`Groups.Add`，并从它的成员身份中删除最后一次连接时将其删除。

没有 API 用于获取组成员身份列表或组的列表。 SignalR 将消息发送到客户端和基于组[发布/订阅模型](http://en.wikipedia.org/wiki/Publish/subscribe)，和服务器不会维护的组或组成员身份列表。 这有助于最大程度提高可伸缩性，因为只要将节点添加到 web 场中，SignalR 维护任何状态已传播到新节点。

<a id="asyncgroupmethods"></a>

### <a name="asynchronous-execution-of-add-and-remove-methods"></a>异步执行 Add 和 Remove 方法

`Groups.Add`和`Groups.Remove`方法以异步方式执行。 如果你想要向组添加客户端并立即将消息发送到客户端使用的组，则必须确保`Groups.Add`方法首先完成。 下面的代码示例演示如何执行该操作。

**向组添加客户端，然后消息传送该客户端**

[!code-csharp[Main](hubs-api-guide-server/samples/sample46.cs?highlight=1,3)]

<a id="grouppersistence"></a>

### <a name="group-membership-persistence"></a>组成员身份暂留

SignalR 跟踪的连接，而不是用户，因此，如果您希望用户为同一组中每次用户建立连接，则必须调用`Groups.Add`每次用户建立新连接。

后的连接暂时中断，有时 SignalR 可以连接自动还原。 在这种情况下，SignalR 还原相同的连接，不建立新连接，并因此客户端的组成员身份将自动恢复。 这是可能甚至临时中断时的结果在服务器重新启动或故障，因为每个客户端，包括组成员身份的连接状态是往返到客户端。 如果一台服务器出现故障时，将替换为新的服务器的连接超时之前，客户端可以自动重新连接到新服务器并重新注册其是成员的组中。

当连接无法自动还原后的连接，断开或连接超时时，或当客户端断开 （例如，当浏览器导航到新页） 时，组成员身份将丢失。 下次用户连接的时将新的连接。 若要维护组成员身份，当同一用户建立新连接时，你的应用程序必须跟踪用户和组之间的关联和还原每次用户建立新连接的组成员身份。

有关连接和重新连接的详细信息，请参阅[如何处理连接生存期事件，Hub 类](#connectionlifetime)本主题中更高版本。

<a id="singleusergroups"></a>

### <a name="single-user-groups"></a>单用户组

应用程序通常使用 SignalR 有来跟踪用户和连接之间的关联这样才能知道哪些用户已发送的消息以及哪些用户应收到一条消息。 实现这一目的，在两种常用模式之一中使用组。

- 单用户组。

    可以指定的用户名与组的名称，并将当前的连接 ID 添加到组，每次用户连接或重新连接。 若要将消息发送到发送到组的用户。 此方法的缺点是组不会为您提供了一种方式找出用户是联机还是脱机。
- 跟踪用户名称和连接 Id 之间的关联。

    可以将每个用户名称和一个或多个连接 Id 之间的关联存储在字典或数据库，并更新每次用户连接或断开连接的存储的数据。 要将消息发送到用户指定连接 Id。 此方法的缺点是它会占用更多内存。

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events-in-the-hub-class"></a>如何处理中心类中的连接生存期事件

处理连接生存期事件的典型原因是用于跟踪是否用户连接，并跟踪的用户名称和连接 Id 之间的关联。 若要运行你自己的代码，当客户端连接或断开连接时，重写`OnConnected`， `OnDisconnected`，和`OnReconnected`类的中心的虚拟方法，如下面的示例中所示。

[!code-csharp[Main](hubs-api-guide-server/samples/sample47.cs?highlight=3,14,22)]

<a id="onreconnected"></a>

### <a name="when-onconnected-ondisconnected-and-onreconnected-are-called"></a>OnConnected、 OnDisconnected 和 OnReconnected 调用时

浏览器导航到新的页上，每次新的连接已建立，这意味着将执行 SignalR`OnDisconnected`方法后跟`OnConnected`方法。 建立新连接时，SignalR 始终创建一个新的连接 ID。

`OnReconnected`当 SignalR 可以从自动恢复，例如当电缆是暂时断开连接并重新连接的连接超时之前的连接已临时中断时调用方法。`OnDisconnected`断开客户端和 SignalR 无法自动重新连接，例如当浏览器导航到新页时调用方法。 因此，一个可能对于给定客户端的事件序列是`OnConnected`， `OnReconnected`， `OnDisconnected`; 或者`OnConnected`， `OnDisconnected`。 不会看到该序列`OnConnected`， `OnDisconnected`，`OnReconnected`为给定的连接。

`OnDisconnected`某些情况下，例如当一台服务器出现故障不会调用方法或应用程序域都能重启。 当另一台服务器随附在行上或在应用程序域完成其回收时，某些客户端可能无法重新连接，并触发`OnReconnected`事件。

有关详细信息，请参阅[理解和 SignalR 中的处理连接生存期事件](handling-connection-lifetime-events.md)。

<a id="nocallerstate"></a>

### <a name="caller-state-not-populated"></a>调用方状态不会填充

在服务器上，这意味着您将放入任何状态称为连接生存期事件处理程序方法`state`客户端上的对象不会填充在`Caller`在服务器上的属性。 有关信息`state`对象和`Caller`属性，请参阅[如何将状态传递客户端和 Hub 类之间](#passstate)本主题中更高版本。

<a id="contextproperty"></a>

## <a name="how-to-get-information-about-the-client-from-the-context-property"></a>如何从上下文属性中获取有关客户端的信息

若要获取有关客户端的信息，请使用`Context`Hub 类的属性。 `Context`属性返回[HubCallerContext](https://msdn.microsoft.com/library/jj890883(v=vs.111).aspx)对象提供了访问权的以下信息：

- 调用客户端连接 ID。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample48.cs?highlight=1)]

    连接 ID 是的 SignalR （不能在你自己的代码中指定值） 分配的 GUID。 不存在的每个连接，以及相同的连接 ID 由所有中心，如果应用程序中有多个中心的一个连接 ID。
- HTTP 标头数据。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample49.cs?highlight=1)]

    此外可以获取来自 HTTP 标头`Context.Headers`。 多个引用相同的操作的原因在于`Context.Headers`首先，创建`Context.Request`更高版本，添加属性和`Context.Headers`被保留用于向后兼容性。
- 查询字符串的数据。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample50.cs?highlight=1)]

    此外可以获取查询字符串数据从`Context.QueryString`。

    在此属性中获取的查询字符串是用于建立 SignalR 连接的 HTTP 请求。 通过配置连接，这是从客户端将有关客户端的数据发送到服务器的简便方法，可以在客户端中添加查询字符串参数。 下面的示例演示使用生成的代理时，JavaScript 客户端中添加的查询字符串的一种方法。

    [!code-javascript[Main](hubs-api-guide-server/samples/sample51.js?highlight=1)]

    有关设置查询字符串参数的详细信息，请参阅针对的 API 指南[JavaScript](hubs-api-guide-javascript-client.md)并[.NET](hubs-api-guide-net-client.md)客户端。

    您可以找到用于查询字符串数据，以及由 SignalR 在内部使用的一些其他值中的连接的传输方法：

    [!code-csharp[Main](hubs-api-guide-server/samples/sample52.cs)]

    值`transportMethod`将是"webSockets"、"serverSentEvents"、"foreverFrame"或"longPolling"。 请注意，如果在签入此值`OnConnected`事件处理程序方法中，在某些情况下可能一开始就会传输该值不是连接的最终协商的传输方法。 在这种情况下该方法将引发异常，并建立最终传输方法时将稍后调用。
- Cookie。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample53.cs?highlight=1)]

    此外可以获取来自 cookie `Context.RequestCookies`。
- 用户信息。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample54.cs?highlight=1)]
- 请求的 HttpContext 对象：

    [!code-csharp[Main](hubs-api-guide-server/samples/sample55.cs?highlight=1)]

    使用此方法，而不会收到`HttpContext.Current`若要获取`HttpContext`SignalR 连接对象。

<a id="passstate"></a>

## <a name="how-to-pass-state-between-clients-and-the-hub-class"></a>如何将客户端和 Hub 类之间传递状态

客户端代理提供`state`对象可以在其中存储要传输到每个方法调用与服务器的数据。 可以在服务器上访问这些数据在`Clients.Caller`的客户端调用集线器方法中的属性。 `Clients.Caller`属性未填充的连接生存期事件处理程序方法`OnConnected`， `OnDisconnected`，和`OnReconnected`。

创建或更新中的数据`state`对象和`Clients.Caller`属性在这两个方向上的工作原理。 它们会传递回客户端和可更新服务器中的值。

下面的示例显示了存储传输到每个方法调用的服务器的状态的 JavaScript 客户端代码。

[!code-javascript[Main](hubs-api-guide-server/samples/sample56.js?highlight=1-2)]

下面的示例显示了.NET 客户端中的等效代码。

[!code-csharp[Main](hubs-api-guide-server/samples/sample57.cs?highlight=1-2)]

在中心类中，可以访问这些数据在`Clients.Caller`属性。 以下示例显示检索到上一示例中所引用的状态的代码。

[!code-csharp[Main](hubs-api-guide-server/samples/sample58.cs?highlight=3-4)]

> [!NOTE]
> 状态持久化此机制不适用于大量数据，因为所有内容将放入`state`或`Clients.Caller`属性是往返与每个方法调用。 它可用于较小的项，如用户名称或计数器。


在 VB.NET 或强类型化的中心，不能通过访问调用方状态对象`Clients.Caller`; 相反，使用`Clients.CallerState`（SignalR 2.1 中引入）：

**在 C# 中使用 CallerState**

[!code-csharp[Main](hubs-api-guide-server/samples/sample59.cs?highlight=3-4)]

**在 Visual Basic 中使用 CallerState**

[!code-vb[Main](hubs-api-guide-server/samples/sample60.vb)]

<a id="handleErrors"></a>

## <a name="how-to-handle-errors-in-the-hub-class"></a>如何处理错误，Hub 类

若要处理你的中心类方法中发生的错误，请首先确保您"观察"异步操作 （如调用客户端方法） 中的任何异常使用`await`。 然后，使用一个或多个以下方法：

- 将方法代码包装在 try catch 块和日志的异常对象。 出于调试目的可以将异常发送到客户端，但出于安全原因的详细的信息发送给在生产环境中的客户端不建议。
- 创建中心管道处理模块时， [OnIncomingError](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubpipelinemodule.onincomingerror(v=vs.111).aspx)方法。 下面的示例演示记录错误，将模块注入到集线器管道的 Startup.cs 中的代码后跟一个管道模块。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample61.cs)]

    [!code-csharp[Main](hubs-api-guide-server/samples/sample62.cs?highlight=4)]
- 使用`HubException`（SignalR 2 中引入） 的类。 可以从任何集线器调用引发此错误。 `HubError`构造函数采用一个字符串消息和一个对象，用于存储额外的错误数据。 SignalR 将自动序列化异常，并将其发送到客户端，其中它将用于拒绝或失败集线器方法调用。

    下面的代码示例演示如何引发`HubException`期间的集线器调用，以及如何处理在 JavaScript 和.NET 客户端上的异常。

    **服务器代码演示 HubException 类**

    [!code-csharp[Main](hubs-api-guide-server/samples/sample63.cs)]

    **演示在一个中心引发 HubException 响应的 JavaScript 客户端代码**

    [!code-html[Main](hubs-api-guide-server/samples/sample64.html)]

    **演示在一个中心引发 HubException 响应的.NET 客户端代码**

    [!code-csharp[Main](hubs-api-guide-server/samples/sample65.cs)]

有关中心管道模块的详细信息，请参阅[如何自定义中心管道](#hubpipeline)本主题中更高版本。

<a id="tracing"></a>

## <a name="how-to-enable-tracing"></a>如何启用跟踪

若要启用服务器端跟踪，system.diagnostics 将元素添加到 Web.config 文件中，在此示例中所示：

[!code-html[Main](hubs-api-guide-server/samples/sample66.html?highlight=17-72)]

在 Visual Studio 中运行应用程序时，可以查看中的日志**输出**窗口。

<a id="callfromoutsidehub"></a>

## <a name="how-to-call-client-methods-and-manage-groups-from-outside-the-hub-class"></a>如何调用方法的客户端和管理的中心类外部的组

若要从 Hub 类比另一个类调用客户端方法，获取集线器的 SignalR 上下文对象的引用并使用它来在客户端上调用方法或管理组。

下面的示例`StockTicker`类获取上下文对象、 将其存储在类的实例，将类实例存储在静态属性，并使用从单一实例类实例的上下文来调用`updateStockPrice`在客户端上的方法连接到名为集线器`StockTickerHub`。

[!code-csharp[Main](hubs-api-guide-server/samples/sample67.cs?highlight=8,24)]

如果需要在长生存期对象中使用上下文多时间，一次获取该引用，并保存它而不是每次重新获取它。 一次获取上下文可确保 SignalR 将消息发送到客户端集线器方法使得这客户端方法调用的相同顺序。 本教程演示如何使用集线器的 SignalR 上下文，请参阅[服务器与 ASP.NET SignalR 广播](../getting-started/tutorial-server-broadcast-with-signalr.md)。

<a id="callingclientsoutsidehub"></a>

### <a name="calling-client-methods"></a>调用客户端方法

你可以指定哪些客户端将接收 RPC，但具有更少选项比从 Hub 类时调用。 这样做的原因是，上下文不相关联的特定调用从客户端，因此任何方法，如需要了解当前的连接 ID `Clients.Others`，或`Clients.Caller`，或`Clients.OthersInGroup`，不可用。 可用选项如下：

- 所有连接的客户端。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample68.cs)]
- 特定的客户端标识的连接 id。

    [!code-css[Main](hubs-api-guide-server/samples/sample69.css)]
- 所有连接的客户端除外指定客户端，由连接 ID 标识。

    [!code-csharp[Main](hubs-api-guide-server/samples/sample70.cs)]
- 指定组中的所有连接的客户端。

    [!code-css[Main](hubs-api-guide-server/samples/sample71.css)]
- 除指定客户端，由连接 id。 指定的组中的所有已连接客户端

    [!code-csharp[Main](hubs-api-guide-server/samples/sample72.cs)]

如果要调用到非中心类从方法中 Hub 类，可以传入当前的连接 ID 并使用与该`Clients.Client`， `Clients.AllExcept`，或`Clients.Group`来模拟`Clients.Caller`， `Clients.Others`，或`Clients.OthersInGroup`。 在以下示例中，`MoveShapeHub`类将传递到的连接 ID`Broadcaster`类，以便`Broadcaster`类来模拟`Clients.Others`。

[!code-csharp[Main](hubs-api-guide-server/samples/sample73.cs?highlight=12,36)]

<a id="managinggroupsoutsidehub"></a>

### <a name="managing-group-membership"></a>管理组成员资格

用于管理组中，Hub 类中一样具有相同的选项。

- 向组添加客户端

    [!code-csharp[Main](hubs-api-guide-server/samples/sample74.cs)]
- 从组中删除客户端

    [!code-css[Main](hubs-api-guide-server/samples/sample75.css)]

<a id="hubpipeline"></a>

## <a name="how-to-customize-the-hubs-pipeline"></a>如何自定义中心管道

SignalR，可将你自己的代码注入到集线器管道。 下面的示例显示了记录从客户端和客户端上调用的传出方法调用中接收每个传入方法调用的自定义中心管道模块：

[!code-csharp[Main](hubs-api-guide-server/samples/sample76.cs)]

下面的代码中*Startup.cs*文件会注册要在中心管道中运行的模块：

[!code-csharp[Main](hubs-api-guide-server/samples/sample77.cs?highlight=3)]

有许多不同的方法可以重写。 有关完整列表，请参阅[HubPipelineModule 方法](https://msdn.microsoft.com/library/jj918633(v=vs.111).aspx)。
