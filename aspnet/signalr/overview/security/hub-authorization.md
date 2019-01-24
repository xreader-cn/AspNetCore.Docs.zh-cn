---
uid: signalr/overview/security/hub-authorization
title: 身份验证和授权 SignalR 集线器 |Microsoft Docs
author: bradygaster
description: 本主题介绍如何限制哪些用户或角色可以访问中心的方法。 本主题中的软件版本使用，Visual Studio 2013.NET 4.5 SignalR ve...
ms.author: bradyg
ms.date: 01/05/2015
ms.assetid: a610c796-c131-473c-baef-2e6c568cb2a2
msc.legacyurl: /signalr/overview/security/hub-authorization
msc.type: authoredcontent
ms.openlocfilehash: bfea212283165facc046e5355571c1e6d9c7cd7d
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837411"
---
<a name="authentication-and-authorization-for-signalr-hubs"></a>身份验证和 SignalR 集线器的授权
====================
通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本主题介绍如何限制哪些用户或角色可以访问中心的方法。
>
> ## <a name="software-versions-used-in-this-topic"></a>本主题中使用的软件版本
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
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

本主题包含以下各节：

- [授权属性](#authorizeattribute)
- [要求所有中心都进行身份验证](#requireauth)
- [自定义的授权](#custom)
- [将身份验证信息传递到客户端](#passauth)
- [.NET 客户端的身份验证选项](#authoptions)

    - [使用窗体身份验证 cookie](#cookie)
    - [Windows 身份验证](#windows)
    - [Connection 标头](#header)
    - [证书](#certificate)

<a id="authorizeattribute"></a>

## <a name="authorize-attribute"></a>授权属性

提供 SignalR [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx)属性来指定哪些用户或角色有权访问的中心或方法。 此属性位于`Microsoft.AspNet.SignalR`命名空间。 您将应用`Authorize`属性为中心或中心中的特定方法。 当应用`Authorize`到 hub 类，指定的授权要求的特性应用于所有集线器中的方法。 本主题提供了不同类型的可以应用的授权要求的示例。 无需`Authorize`属性，则连接客户端可以访问任何公共方法的中心。

如果已定义 web 应用程序中名为"Admin"的角色，则可以指定该角色中的唯一用户可以访问用下面的代码中心。

[!code-csharp[Main](hub-authorization/samples/sample1.cs)]

或者，你可以指定一个中心包含可供所有用户的一种方法和仅供经过身份验证的用户，如下所示的第二个方法。

[!code-csharp[Main](hub-authorization/samples/sample2.cs)]

下面的示例解决不同的授权方案：

- `[Authorize]` – 仅经过身份验证的用户
- `[Authorize(Roles = "Admin,Manager")]` – 仅经过身份验证中的指定角色的用户
- `[Authorize(Users = "user1,user2")]` – 仅经过身份验证具有指定的用户名的用户
- `[Authorize(RequireOutgoing=false)]` – 仅经过身份验证的用户可以调用中心，但从服务器返回到客户端的调用不局限于授权，例如，只有特定用户可以发送一条消息，但所有其他人可以接收消息时。 RequireOutgoing 属性只能应用到整个的中心，不在中心内的个人方法。 如果 RequireOutgoing 未设置为 false，满足授权要求的用户将调用从服务器中。

<a id="requireauth"></a>

## <a name="require-authentication-for-all-hubs"></a>要求所有中心都进行身份验证

你可以要求身份验证的所有集线器和集线器方法在应用程序中通过调用[RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx)方法时在应用程序启动。 当你具有多个中心并想要为所有这些强制实施的身份验证要求时，可能会使用此方法。 使用此方法，不能指定角色、 用户或传出的授权的要求。 仅可以指定到集线器方法的访问权限仅限于经过身份验证的用户。 但是，仍可应用到中心或方法，以指定其他要求的 Authorize 属性。 在属性中指定的任何要求添加到身份验证的基本要求。

下面的示例显示了将所有集线器方法都限制给已经过身份验证的用户的启动文件。

[!code-csharp[Main](hub-authorization/samples/sample3.cs)]

如果您调用`RequireAuthentication()`方法处理 SignalR 请求后，将引发 SignalR`InvalidOperationException`异常。 因为不能将模块添加到 HubPipeline 调用管道后，SignalR 会引发此异常。 上面的示例演示调用`RequireAuthentication`中的方法`Configuration`方法执行之前处理的第一个请求一次。

<a id="custom"></a>

## <a name="customized-authorization"></a>自定义的授权

如果需要自定义如何确定授权，则可以创建派生的类`AuthorizeAttribute`并重写[UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx)方法。 对于每个请求，SignalR 调用此方法来确定是否授权用户来完成该请求。 在重写方法中，为你的授权方案提供必要的逻辑。 下面的示例演示如何强制实施通过基于声明的标识的授权。

[!code-csharp[Main](hub-authorization/samples/sample4.cs)]

<a id="passauth"></a>

## <a name="pass-authentication-information-to-clients"></a>将身份验证信息传递到客户端

您可能需要在客户端运行的代码中使用的身份验证信息。 在客户端上调用方法时传递所需的信息。 例如，聊天应用程序方法可以作为参数传递发布一条消息的人员的用户名称，如下所示。

[!code-csharp[Main](hub-authorization/samples/sample5.cs)]

或者，可以创建表示身份验证信息并将该对象作为参数传递的对象，如下所示。

[!code-csharp[Main](hub-authorization/samples/sample6.cs)]

因为恶意用户无法使用它来模拟该客户端的请求，应永远不会将一台客户端连接 id 传递到其他客户端。

<a id="authoptions"></a>

## <a name="authentication-options-for-net-clients"></a>.NET 客户端的身份验证选项

当具有.NET 客户端，例如一个控制台应用，用于与集线器被限制为身份验证的用户交互时，您可以传递中 cookie、 connection 标头或证书的身份验证凭据。 在本部分中的示例演示如何使用这些不同的方法对用户进行身份验证。 它们不是完全正常运行的 SignalR 应用程序。 有关使用 SignalR.NET 客户端的详细信息，请参阅[中心 API 指南-.NET 客户端](../guide-to-the-api/hubs-api-guide-net-client.md)。

<a id="cookie"></a>

### <a name="cookie"></a>Cookie

在.NET 客户端与使用 ASP.NET 窗体身份验证的中心进行交互，您将需要手动将身份验证 cookie 设置在连接上。 添加到 cookie`CookieContainer`上的属性[HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx)对象。 下面的示例演示一个控制台应用，在网页上检索身份验证 cookie 并将该 cookie 添加到连接。

[!code-csharp[Main](hub-authorization/samples/sample7.cs)]

此控制台应用发布到的凭据<strong>www.contoso.com/RemoteLogin</strong>其可以引用包含下面的代码隐藏文件的空页。

[!code-csharp[Main](hub-authorization/samples/sample8.cs)]

<a id="windows"></a>

### <a name="windows-authentication"></a>Windows 身份验证

当使用 Windows 身份验证，您可以通过传递当前用户的凭据[在](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx)属性。 在的值设置为连接的凭据。

[!code-csharp[Main](hub-authorization/samples/sample9.cs?highlight=6)]

<a id="header"></a>

### <a name="connection-header"></a>Connection 标头

如果你的应用程序不使用 cookie，则可以连接标头中传递用户信息。 例如，你可以连接标头中传递一个令牌。

[!code-csharp[Main](hub-authorization/samples/sample10.cs?highlight=6)]

然后，在中心，您将验证用户的令牌。

<a id="certificate"></a>

### <a name="certificate"></a>证书

可以传递要验证的用户的客户端证书。 创建连接时添加的证书。 下面的示例演示如何将客户端证书添加到连接;它不显示完整的控制台应用。 它使用[X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx)类提供多种不同的方式来创建的证书。

[!code-csharp[Main](hub-authorization/samples/sample11.cs?highlight=6)]
