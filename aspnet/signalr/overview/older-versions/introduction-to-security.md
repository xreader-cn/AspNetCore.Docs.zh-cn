---
uid: signalr/overview/older-versions/introduction-to-security
title: SignalR 安全性简介 (SignalR 1.x) |Microsoft Docs
author: bradygaster
description: 介绍开发 SignalR 应用程序时必须考虑的安全问题。
ms.author: bradyg
ms.date: 10/17/2013
ms.assetid: 715a4059-d307-4631-abbb-c789c95d6eb4
msc.legacyurl: /signalr/overview/older-versions/introduction-to-security
msc.type: authoredcontent
ms.openlocfilehash: 94a4598595d025267c69933a03be2c164066cc98
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837801"
---
<a name="introduction-to-signalr-security-signalr-1x"></a>SignalR 安全性简介 (SignalR 1.x)
====================
通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本文介绍开发 SignalR 应用程序时必须考虑的安全问题。


## <a name="overview"></a>概述

本文档包含以下各节：

- [SignalR 安全性概念](#concepts)

    - [身份验证和授权](#authentication)
    - [连接令牌](#connectiontoken)
    - [重新连接时重新加入组](#rejoingroup)
- [SignalR 是如何阻止跨站点请求伪造](#csrf)
- [SignalR 的安全建议](#recommendations)

    - [安全套接字层 (SSL) 协议](#ssl)
    - [不将组用作一种安全机制](#groupsecurity)
    - [安全地处理来自客户端的输入](#input)
    - [协调与活动连接的用户状态中的更改](#reconcile)
    - [自动生成的 JavaScript 代理文件](#autogen)
    - [异常](#exceptions)

<a id="concepts"></a>

## <a name="signalr-security-concepts"></a>SignalR 安全性概念

<a id="authentication"></a>

### <a name="authentication-and-authorization"></a>身份验证和授权

SignalR 设计为可集成到现有应用程序的身份验证结构。 它不提供任何功能的用户进行身份验证。 相反，您进行身份验证的用户通常会在应用程序中，然后使用 SignalR 代码中的身份验证的结果。 例如，可能会使用 ASP.NET 窗体身份验证，对用户身份验证，然后在中心，会强制哪些用户或角色有权调用的方法。 在中心，也可以传递身份验证信息，如用户名或用户是否属于一个角色，向客户端。

提供 SignalR [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx)属性来指定哪些用户有权访问的中心或方法。 将 Authorize 属性应用到一个中心或中心中的特定方法。 如果不使用 Authorize 属性，中心上的所有公共方法可供连接到集线器的客户端。 有关中心的详细信息，请参阅[身份验证和授权 SignalR 集线器](../security/hub-authorization.md)。

`Authorize`属性仅用于中心。 若要使用时强制实施授权规则`PersistentConnection`必须重写`AuthorizeRequest`方法。 有关持久连接的详细信息，请参阅[身份验证和授权 SignalR 永久性连接](../security/persistent-connection-authorization.md)。

<a id="connectiontoken"></a>

### <a name="connection-token"></a>连接令牌

SignalR 应执行恶意命令验证发件人标识的风险。 包含的连接 id 和身份验证的用户的用户名的连接令牌为每个请求客户端和服务器之间传递。 连接 id 是随机生成服务器时的新连接创建，并且连接的持续时间内保持不变的唯一标识符。 由 web 应用程序的身份验证机制提供用户名。 加密和数字签名进行保护的连接令牌。

![](introduction-to-security/_static/image2.png)

对于每个请求，服务器来验证该令牌，确保该请求来自指定的用户的内容。 用户名必须对应于连接 id。通过验证的连接 id 和用户名，SignalR 会阻止恶意用户轻松地模拟另一个用户。 如果服务器无法验证的连接令牌，则请求将失败。

![](introduction-to-security/_static/image4.png)

连接 id 是验证过程的一部分，因为不应显示一个用户的连接 id 向其他用户或在客户端上的值如存储在一个 cookie。

<a id="rejoingroup"></a>

### <a name="rejoining-groups-when-reconnecting"></a>重新连接时重新加入组

默认情况下，SignalR 应用程序将自动重新将用户分配到相应的组的临时中断后，如删除并重新建立了连接超时之前连接重新连接时。当重新连接后，客户端传递包括连接 id 和分配的组的组令牌。 组令牌进行数字签名和加密。 客户端后重新连接; 保留相同的连接 id因此，重新连接客户端传递的连接 id 必须匹配上一个客户端使用的连接 id。 此验证会阻止恶意用户将请求加入未经授权的组重新连接时传递。

但是，务必要注意，组令牌不会过期。 如果用户在过去，属于某个组，但已阻止从该组，该用户可能能够模拟包含被禁止的组的组令牌。 如果您需要安全地管理哪些用户属于哪些组，您需要将该数据的服务器上，如存储在数据库中。 然后，将逻辑添加到你的应用程序，用于在服务器上验证用户是否属于一个组。 验证组成员身份的示例，请参阅[使用组](../guide-to-the-api/working-with-groups.md)。

自动重新加入组仅适用于连接暂时中断后重新连接。 如果用户断开连接通过导航离开该应用程序或应用程序重新启动，你的应用程序必须处理如何将该用户添加到正确的组。 有关详细信息，请参阅[使用组](../guide-to-the-api/working-with-groups.md)。

<a id="csrf"></a>

## <a name="how-signalr-prevents-cross-site-request-forgery"></a>SignalR 是如何阻止跨站点请求伪造

跨站点请求伪造 (CSRF) 是一种攻击，恶意站点发送请求到易受攻击的站点在当前登录用户。 SignalR 防止 CSRF 通过使恶意站点来创建有效的请求的 SignalR 应用程序的情况极其少见。

### <a name="description-of-csrf-attack"></a>CSRF 攻击的说明

下面是 CSRF 攻击的一个例子：

1. 用户登录到`www.example.com`，使用窗体身份验证。
2. 服务器对用户进行身份验证。 从服务器响应包括身份验证 cookie。
3. 而无需注销，用户访问恶意网站。 此恶意站点包含以下 HTML 窗体： 

    [!code-html[Main](introduction-to-security/samples/sample1.html)]

   请注意，窗体操作发到易受攻击的站点，不到的恶意站点。 这是 CSRF 的"跨站点"部分。
4. 用户单击提交按钮。 在浏览器包括与请求的身份验证 cookie。
5. 请求与用户的身份验证上下文，在 example.com 服务器上运行，并可以执行任何操作允许身份验证的用户执行的操作。

虽然此示例需要用户单击窗体按钮，但恶意页面可以同样轻松地运行 AJAX 请求发送到 SignalR 应用程序的脚本。 此外，使用 SSL 不会阻止实施 CSRF 攻击，因为恶意站点可以发送"https://"请求。

通常情况下，CSRF 攻击是可能对网站使用 cookie 进行身份验证，因为浏览器向目标网站发送所有相关 cookie。 但是，CSRF 攻击并不局限于利用 cookie。 例如，基本和摘要式身份验证也是易受攻击的。 使用基本或摘要式身份验证的用户登录后，浏览器自动发送凭据，直到会话结束。

### <a name="csrf-mitigations-taken-by-signalr"></a>SignalR 所执行的 CSRF 缓解措施

SignalR 将执行以下步骤来阻止恶意站点创建到 SignalR 应用程序的有效请求。 默认情况下执行这些步骤，并不需要在代码中的任何操作。

- **禁用跨域请求**  
 默认情况下，跨域请求中将禁用 SignalR 应用程序以防止用户从外部域中调用 SignalR 终结点。 来自外部域的任何请求将自动被视为无效并被阻止。 建议保留此默认行为;否则，恶意站点无法欺骗用户在将命令发送到你的站点。 如果您需要使用跨域请求，请参阅[如何建立跨域连接](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain)。
- **传递查询字符串，不 cookie 中的连接令牌**  
 SignalR 传递的连接作为查询字符串值，而不是作为 cookie。 通过不存储的连接令牌作为 cookie 的连接令牌不会无意中转发由浏览器时遇到恶意代码。 此外，不会超出当前连接保留的连接令牌。 因此，恶意用户不能在另一个用户的身份验证凭据的请求。
- **验证连接令牌**  
 如中所述[连接令牌](#connectiontoken)部分中，服务器就知道哪个连接 id 是与每个经过身份验证的用户相关联。 服务器不会处理来自用户名称不匹配的连接 id 的任何请求。 不太可能因为恶意用户需要知道用户名称和当前的随机生成连接 id，恶意用户无法猜出有效的请求。只要该连接结束，该连接 id 将变为无效。 匿名用户不应有权访问任何敏感信息。

<a id="recommendations"></a>

## <a name="signalr-security-recommendations"></a>SignalR 的安全建议

<a id="ssl"></a>

### <a name="secure-socket-layers-ssl-protocol"></a>安全套接字层 (SSL) 协议

SSL 协议使用加密来保护客户端和服务器之间的数据传输。 如果你的 SignalR 应用程序之间传输敏感信息的客户端和服务器，使用 SSL 进行传输。 有关设置 SSL 的详细信息，请参阅[如何设置 IIS 7 上的 SSL](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis)。

<a id="groupsecurity"></a>

### <a name="do-not-use-groups-as-a-security-mechanism"></a>不将组用作一种安全机制

组是方便的收集相关的用户，但它们不是一个安全机制用于限制对敏感信息的访问。 当用户可以自动重新加入组重新连接期间，也是如此。 相反，请考虑向角色添加权限的用户，并限制对该角色的成员的集线器方法的访问。 有关基于角色限制访问的示例，请参阅[身份验证和授权 SignalR 集线器](../security/hub-authorization.md)。 检查用户访问组重新连接时的示例，请参阅[使用组](../guide-to-the-api/working-with-groups.md)。

<a id="input"></a>

### <a name="safely-handling-input-from-clients"></a>安全地处理来自客户端的输入

从客户端适用于广播到其他客户端的所有输入必须进行都编码，以确保恶意用户不向其他用户发送脚本。 它最好在接收客户端而非在服务器上的消息进行编码，因为 SignalR 应用程序可能具有许多不同类型的客户端。 因此，HTML 编码的工作有关 web 客户端，而不是其他类型的客户端。 例如，若要显示的聊天消息的 web 客户端方法将安全地处理用户名称和消息通过调用`html()`函数。

[!code-html[Main](introduction-to-security/samples/sample2.html?highlight=3-4)]

<a id="reconcile"></a>

### <a name="reconciling-a-change-in-user-status-with-an-active-connection"></a>协调与活动连接的用户状态中的更改

如果用户的身份验证状态发生更改的活动连接存在时，用户将收到一条指出错误、"用户标识不能更改活动的 SignalR 连接期间。" 在这种情况下，你的应用程序应重新连接到服务器，以确保连接 id 和用户名进行协调。 例如，如果你的应用程序允许用户注销的活动连接存在时，该连接的用户名将不再匹配下一个请求传入的名称。 将想要在用户注销之前停止连接，然后重新启动它。

但是，务必要注意，大多数应用程序不需要手动停止和启动连接。 如果你的应用程序用户重定向到单独的页面后日志记录，如 Web 窗体应用程序或 MVC 应用程序中的默认行为，或者注销后刷新当前页，自动断开连接的活动连接，而不需要执行任何其他操作。

下面的示例演示如何停止和启动连接时，用户状态已更改。

[!code-html[Main](introduction-to-security/samples/sample3.html)]

或者，如果您的网站通过窗体身份验证，使用可调到期，并且没有任何活动保持有效的身份验证 cookie，可能会更改用户的身份验证状态。 在这种情况下，将注销用户和用户名称将不再匹配的连接令牌中的用户名称。 可以通过添加一些定期请求的资源，在 web 服务器以保持身份验证 cookie 有效的脚本来解决此问题。 下面的示例演示如何请求每隔 30 分钟的资源。

[!code-javascript[Main](introduction-to-security/samples/sample4.js)]

<a id="autogen"></a>

### <a name="automatically-generated-javascript-proxy-files"></a>自动生成的 JavaScript 代理文件

如果您不想要在每个用户的 JavaScript 代理文件中包含所有集线器和方法，可以禁用自动生成相应的文件。 如果您具有多个中心和方法，但不是希望每个用户需要注意的所有方法，可以选择此选项。 通过设置禁用自动生成**EnableJavaScriptProxies**到**false**。

[!code-csharp[Main](introduction-to-security/samples/sample5.cs)]

有关 JavaScript 代理文件的详细信息，请参阅[生成的代理和它为您完成](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy)。 <a id="exceptions"></a>

### <a name="exceptions"></a>Exceptions

应避免将异常对象传递到客户端，因为对象可能会公开给客户端的敏感信息。 相反，在显示的相关错误消息的客户端上调用方法。

[!code-csharp[Main](introduction-to-security/samples/sample6.cs)]
