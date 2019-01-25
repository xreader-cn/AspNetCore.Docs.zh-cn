---
uid: signalr/overview/security/introduction-to-security
title: SignalR 安全性简介 |Microsoft Docs
author: bradygaster
description: 介绍开发 SignalR 应用程序时必须考虑的安全问题。
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: ed562717-8591-4936-8e10-c7e63dcb570a
msc.legacyurl: /signalr/overview/security/introduction-to-security
msc.type: authoredcontent
ms.openlocfilehash: 311bfb4279b1c919fddcef2c0aed657083f9c34f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837320"
---
<a name="introduction-to-signalr-security"></a><span data-ttu-id="d9355-103">SignalR 安全性简介</span><span class="sxs-lookup"><span data-stu-id="d9355-103">Introduction to SignalR Security</span></span>
====================
<span data-ttu-id="d9355-104">通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="d9355-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="d9355-105">本文介绍开发 SignalR 应用程序时必须考虑的安全问题。</span><span class="sxs-lookup"><span data-stu-id="d9355-105">This article describes the security issues you must consider when developing a SignalR application.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="d9355-106">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="d9355-106">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="d9355-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="d9355-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="d9355-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="d9355-108">.NET 4.5</span></span>
> - <span data-ttu-id="d9355-109">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="d9355-109">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="d9355-110">本主题的早期版本</span><span class="sxs-lookup"><span data-stu-id="d9355-110">Previous versions of this topic</span></span>
>
> <span data-ttu-id="d9355-111">有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。</span><span class="sxs-lookup"><span data-stu-id="d9355-111">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="d9355-112">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="d9355-112">Questions and comments</span></span>
>
> <span data-ttu-id="d9355-113">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="d9355-113">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="d9355-114">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="d9355-114">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="d9355-115">概述</span><span class="sxs-lookup"><span data-stu-id="d9355-115">Overview</span></span>

<span data-ttu-id="d9355-116">本文档包含以下各节：</span><span class="sxs-lookup"><span data-stu-id="d9355-116">This document contains the following sections:</span></span>

- [<span data-ttu-id="d9355-117">SignalR 安全性概念</span><span class="sxs-lookup"><span data-stu-id="d9355-117">SignalR Security Concepts</span></span>](#concepts)

    - [<span data-ttu-id="d9355-118">身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="d9355-118">Authentication and authorization</span></span>](#authentication)
    - [<span data-ttu-id="d9355-119">连接令牌</span><span class="sxs-lookup"><span data-stu-id="d9355-119">Connection token</span></span>](#connectiontoken)
    - [<span data-ttu-id="d9355-120">重新连接时重新加入组</span><span class="sxs-lookup"><span data-stu-id="d9355-120">Rejoining groups when reconnecting</span></span>](#rejoingroup)
- [<span data-ttu-id="d9355-121">SignalR 是如何阻止跨站点请求伪造</span><span class="sxs-lookup"><span data-stu-id="d9355-121">How SignalR prevents Cross-Site Request Forgery</span></span>](#csrf)
- [<span data-ttu-id="d9355-122">SignalR 的安全建议</span><span class="sxs-lookup"><span data-stu-id="d9355-122">SignalR Security Recommendations</span></span>](#recommendations)

    - [<span data-ttu-id="d9355-123">安全套接字层 (SSL) 协议</span><span class="sxs-lookup"><span data-stu-id="d9355-123">Secure Socket Layers (SSL) protocol</span></span>](#ssl)
    - [<span data-ttu-id="d9355-124">不将组用作一种安全机制</span><span class="sxs-lookup"><span data-stu-id="d9355-124">Do not use groups as a security mechanism</span></span>](#groupsecurity)
    - [<span data-ttu-id="d9355-125">安全地处理来自客户端的输入</span><span class="sxs-lookup"><span data-stu-id="d9355-125">Safely handling input from clients</span></span>](#input)
    - [<span data-ttu-id="d9355-126">协调与活动连接的用户状态中的更改</span><span class="sxs-lookup"><span data-stu-id="d9355-126">Reconciling a change in user status with an active connection</span></span>](#reconcile)
    - [<span data-ttu-id="d9355-127">自动生成的 JavaScript 代理文件</span><span class="sxs-lookup"><span data-stu-id="d9355-127">Automatically generated JavaScript proxy files</span></span>](#autogen)
    - [<span data-ttu-id="d9355-128">异常</span><span class="sxs-lookup"><span data-stu-id="d9355-128">Exceptions</span></span>](#exceptions)

<a id="concepts"></a>

## <a name="signalr-security-concepts"></a><span data-ttu-id="d9355-129">SignalR 安全性概念</span><span class="sxs-lookup"><span data-stu-id="d9355-129">SignalR Security Concepts</span></span>

<a id="authentication"></a>

### <a name="authentication-and-authorization"></a><span data-ttu-id="d9355-130">身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="d9355-130">Authentication and authorization</span></span>

<span data-ttu-id="d9355-131">用户进行身份验证，SignalR 不提供任何功能。</span><span class="sxs-lookup"><span data-stu-id="d9355-131">SignalR does not provide any features for authenticating users.</span></span> <span data-ttu-id="d9355-132">相反，将 SignalR 功能集成到应用程序的现有身份验证结构。</span><span class="sxs-lookup"><span data-stu-id="d9355-132">Instead, you integrate the SignalR features into the existing authentication structure for an application.</span></span> <span data-ttu-id="d9355-133">您进行身份验证的用户将在您的应用程序，并且可在 SignalR 中的身份验证的结果代码。</span><span class="sxs-lookup"><span data-stu-id="d9355-133">You authenticate users as you would normally in your application, and work with the results of the authentication in your SignalR code.</span></span> <span data-ttu-id="d9355-134">例如，可能会使用 ASP.NET 窗体身份验证，对用户身份验证，然后在中心，会强制哪些用户或角色有权调用的方法。</span><span class="sxs-lookup"><span data-stu-id="d9355-134">For example, you might authenticate your users with ASP.NET forms authentication, and then in your hub, enforce which users or roles are authorized to call a method.</span></span> <span data-ttu-id="d9355-135">在中心，也可以传递身份验证信息，如用户名或用户是否属于一个角色，向客户端。</span><span class="sxs-lookup"><span data-stu-id="d9355-135">In your hub, you can also pass authentication information, such as user name or whether a user belongs to a role, to the client.</span></span>

<span data-ttu-id="d9355-136">提供 SignalR [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx)属性来指定哪些用户有权访问的中心或方法。</span><span class="sxs-lookup"><span data-stu-id="d9355-136">SignalR provides the [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) attribute to specify which users have access to a hub or method.</span></span> <span data-ttu-id="d9355-137">将 Authorize 属性应用到一个中心或中心中的特定方法。</span><span class="sxs-lookup"><span data-stu-id="d9355-137">You apply the Authorize attribute to either a hub or particular methods in a hub.</span></span> <span data-ttu-id="d9355-138">如果不使用 Authorize 属性，中心上的所有公共方法可供连接到集线器的客户端。</span><span class="sxs-lookup"><span data-stu-id="d9355-138">Without the Authorize attribute, all public methods on the hub are available to a client that is connected to the hub.</span></span> <span data-ttu-id="d9355-139">有关中心的详细信息，请参阅[身份验证和授权 SignalR 集线器](hub-authorization.md)。</span><span class="sxs-lookup"><span data-stu-id="d9355-139">For more information about hubs, see [Authentication and Authorization for SignalR Hubs](hub-authorization.md).</span></span>

<span data-ttu-id="d9355-140">您将应用`Authorize`属性为中心，但没有永久连接。</span><span class="sxs-lookup"><span data-stu-id="d9355-140">You apply the `Authorize` attribute to hubs, but not persistent connections.</span></span> <span data-ttu-id="d9355-141">若要使用时强制实施授权规则`PersistentConnection`必须重写`AuthorizeRequest`方法。</span><span class="sxs-lookup"><span data-stu-id="d9355-141">To enforce authorization rules when using a `PersistentConnection` you must override the `AuthorizeRequest` method.</span></span> <span data-ttu-id="d9355-142">有关持久连接的详细信息，请参阅[身份验证和授权 SignalR 永久性连接](persistent-connection-authorization.md)。</span><span class="sxs-lookup"><span data-stu-id="d9355-142">For more information about persistent connections, see [Authentication and Authorization for SignalR Persistent Connections](persistent-connection-authorization.md).</span></span>

<a id="connectiontoken"></a>

### <a name="connection-token"></a><span data-ttu-id="d9355-143">连接令牌</span><span class="sxs-lookup"><span data-stu-id="d9355-143">Connection token</span></span>

<span data-ttu-id="d9355-144">SignalR 应执行恶意命令验证发件人标识的风险。</span><span class="sxs-lookup"><span data-stu-id="d9355-144">SignalR mitigates the risk of executing malicious commands by validating the identity of the sender.</span></span> <span data-ttu-id="d9355-145">对于每个请求，客户端和服务器传递的连接令牌，其中包含连接 id 和身份验证的用户的用户名。</span><span class="sxs-lookup"><span data-stu-id="d9355-145">For each request, the client and server pass a connection token which contains the connection id and username for authenticated users.</span></span> <span data-ttu-id="d9355-146">连接 id 唯一地标识每个连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="d9355-146">The connection id uniquely identifies each connected client.</span></span> <span data-ttu-id="d9355-147">新的连接创建后，且在连接期间会一直保持该 id 时，服务器随机生成的连接 id。</span><span class="sxs-lookup"><span data-stu-id="d9355-147">The server randomly generates the connection id when a new connection is created, and persists that id for the duration of the connection.</span></span> <span data-ttu-id="d9355-148">Web 应用程序的身份验证机制提供用户名。</span><span class="sxs-lookup"><span data-stu-id="d9355-148">The authentication mechanism for the web application provides the username.</span></span> <span data-ttu-id="d9355-149">SignalR 使用加密和数字签名来保护的连接令牌。</span><span class="sxs-lookup"><span data-stu-id="d9355-149">SignalR uses encryption and a digital signature to protect the connection token.</span></span>

![](introduction-to-security/_static/image2.png)

<span data-ttu-id="d9355-150">对于每个请求，服务器来验证该令牌，确保该请求来自指定的用户的内容。</span><span class="sxs-lookup"><span data-stu-id="d9355-150">For each request, the server validates the contents of the token to ensure that the request is coming from the specified user.</span></span> <span data-ttu-id="d9355-151">用户名必须对应于连接 id。通过验证的连接 id 和用户名，SignalR 会阻止恶意用户轻松地模拟另一个用户。</span><span class="sxs-lookup"><span data-stu-id="d9355-151">The username must correspond to the connection id. By validating both the connection id and the username, SignalR prevents a malicious user from easily impersonating another user.</span></span> <span data-ttu-id="d9355-152">如果服务器无法验证的连接令牌，则请求将失败。</span><span class="sxs-lookup"><span data-stu-id="d9355-152">If the server cannot validate the connection token, the request fails.</span></span>

![](introduction-to-security/_static/image4.png)

<span data-ttu-id="d9355-153">连接 id 是验证过程的一部分，因为不应显示一个用户的连接 id 向其他用户或在客户端上的值如存储在一个 cookie。</span><span class="sxs-lookup"><span data-stu-id="d9355-153">Because the connection id is part of the verification process, you should not reveal one user's connection id to other users or store the value on the client, such as in a cookie.</span></span>

#### <a name="connection-tokens-vs-other-token-types"></a><span data-ttu-id="d9355-154">与其他令牌类型的连接令牌</span><span class="sxs-lookup"><span data-stu-id="d9355-154">Connection tokens vs. other token types</span></span>

<span data-ttu-id="d9355-155">安全工具的连接令牌有时标记，因为它们显示为会话令牌或身份验证令牌，如果公开，会带来风险。</span><span class="sxs-lookup"><span data-stu-id="d9355-155">Connection tokens are occasionally flagged by security tools because they appear to be session tokens or authentication tokens, which poses a risk if exposed.</span></span>

<span data-ttu-id="d9355-156">SignalR 的连接令牌不是身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="d9355-156">SignalR's connection token isn't an authentication token.</span></span> <span data-ttu-id="d9355-157">它用于确认发出此请求的用户是同一个创建了连接。</span><span class="sxs-lookup"><span data-stu-id="d9355-157">It is used to confirm that the user making this request is the same one that created the connection.</span></span> <span data-ttu-id="d9355-158">连接令牌是必需的因为 ASP.NET SignalR 允许连接的服务器之间移动。</span><span class="sxs-lookup"><span data-stu-id="d9355-158">The connection token is necessary because ASP.NET SignalR allows connections to move between servers.</span></span> <span data-ttu-id="d9355-159">令牌将连接与特定用户相关联，但不声明发出请求的用户的标识。</span><span class="sxs-lookup"><span data-stu-id="d9355-159">The token associates the connection with a particular user but doesn't assert the identity of the user making the request.</span></span> <span data-ttu-id="d9355-160">SignalR 请求正确经过身份验证，它必须有一些其他令牌断言的标识的用户，例如 cookie 或持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="d9355-160">For a SignalR request to be properly authenticated, it must have some other token that asserts the identity of the user, such as a cookie or bearer token.</span></span> <span data-ttu-id="d9355-161">但是，连接令牌本身可以由该用户，仅的令牌中包含的连接 ID 发出请求的任何索赔是与该用户关联。</span><span class="sxs-lookup"><span data-stu-id="d9355-161">However, the connection token itself makes no claim that the request was made by that user, only that the connection ID contained within the token is associated with that user.</span></span>

<span data-ttu-id="d9355-162">由于连接令牌不提供其自己的任何身份验证声明，它不会被视为"会话"或"身份验证"令牌。</span><span class="sxs-lookup"><span data-stu-id="d9355-162">Since the connection token provides no authentication claim of its own, it isn't considered a "session" or "authentication" token.</span></span> <span data-ttu-id="d9355-163">获取给定的用户的连接令牌并将重播请求以其他用户进行身份验证 （或未经身份验证的请求） 中将失败，因为请求的用户标识和存储在令牌中的标识不匹配。</span><span class="sxs-lookup"><span data-stu-id="d9355-163">Taking a given user's connection token and replaying it in a request authenticated as a different user (or an unauthenticated request) will fail, because the user identity of the request and the identity stored in the token won't match.</span></span>

<a id="rejoingroup"></a>

### <a name="rejoining-groups-when-reconnecting"></a><span data-ttu-id="d9355-164">重新连接时重新加入组</span><span class="sxs-lookup"><span data-stu-id="d9355-164">Rejoining groups when reconnecting</span></span>

<span data-ttu-id="d9355-165">默认情况下，SignalR 应用程序将自动重新将用户分配到相应的组的临时中断后，如删除并重新建立了连接超时之前连接重新连接时。当重新连接后，客户端传递包括连接 id 和分配的组的组令牌。</span><span class="sxs-lookup"><span data-stu-id="d9355-165">By default, the SignalR application will automatically re-assign a user to the appropriate groups when reconnecting from a temporary disruption, such as when a connection is dropped and re-established before the connection times out. When reconnecting, the client passes a group token that includes the connection id and the assigned groups.</span></span> <span data-ttu-id="d9355-166">组令牌进行数字签名和加密。</span><span class="sxs-lookup"><span data-stu-id="d9355-166">The group token is digitally signed and encrypted.</span></span> <span data-ttu-id="d9355-167">客户端后重新连接; 保留相同的连接 id因此，重新连接客户端传递的连接 id 必须匹配上一个客户端使用的连接 id。</span><span class="sxs-lookup"><span data-stu-id="d9355-167">The client retains the same connection id after a reconnection; therefore, the connection id passed from the reconnected client must match the previous connection id used by the client.</span></span> <span data-ttu-id="d9355-168">此验证会阻止恶意用户将请求加入未经授权的组重新连接时传递。</span><span class="sxs-lookup"><span data-stu-id="d9355-168">This verification prevents a malicious user from passing requests to join unauthorized groups when reconnecting.</span></span>

<span data-ttu-id="d9355-169">但是，务必要注意，组令牌不会过期。</span><span class="sxs-lookup"><span data-stu-id="d9355-169">However, it's important to note, that the group token does not expire.</span></span> <span data-ttu-id="d9355-170">如果用户在过去，属于某个组，但已阻止从该组，该用户可能能够模拟包含被禁止的组的组令牌。</span><span class="sxs-lookup"><span data-stu-id="d9355-170">If a user belonged to a group in the past, but was banned from that group, that user may be able to mimic a group token that includes the prohibited group.</span></span> <span data-ttu-id="d9355-171">如果您需要安全地管理哪些用户属于哪些组，您需要将该数据的服务器上，如存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="d9355-171">If you need to securely manage which users belong to which groups, you need to store that data on the server, such as in a database.</span></span> <span data-ttu-id="d9355-172">然后，将逻辑添加到你的应用程序，用于在服务器上验证用户是否属于一个组。</span><span class="sxs-lookup"><span data-stu-id="d9355-172">Then, add logic to your application that verifies on the server whether a user belongs to a group.</span></span> <span data-ttu-id="d9355-173">验证组成员身份的示例，请参阅[使用组](../guide-to-the-api/working-with-groups.md)。</span><span class="sxs-lookup"><span data-stu-id="d9355-173">For an example of verifying group membership, see [Working with groups](../guide-to-the-api/working-with-groups.md).</span></span>

<span data-ttu-id="d9355-174">自动重新加入组仅适用于连接暂时中断后重新连接。</span><span class="sxs-lookup"><span data-stu-id="d9355-174">Automatically rejoining groups only applies when a connection is reconnected after a temporary disruption.</span></span> <span data-ttu-id="d9355-175">如果用户断开连接通过导航离开该应用程序或应用程序重新启动，你的应用程序必须处理如何将该用户添加到正确的组。</span><span class="sxs-lookup"><span data-stu-id="d9355-175">If a user disconnects by navigating away from the application or the application restarts, your application must handle how to add that user to the correct groups.</span></span> <span data-ttu-id="d9355-176">有关详细信息，请参阅[使用组](../guide-to-the-api/working-with-groups.md)。</span><span class="sxs-lookup"><span data-stu-id="d9355-176">For more information, see [Working with groups](../guide-to-the-api/working-with-groups.md).</span></span>

<a id="csrf"></a>

## <a name="how-signalr-prevents-cross-site-request-forgery"></a><span data-ttu-id="d9355-177">SignalR 是如何阻止跨站点请求伪造</span><span class="sxs-lookup"><span data-stu-id="d9355-177">How SignalR prevents Cross-Site Request Forgery</span></span>

<span data-ttu-id="d9355-178">跨站点请求伪造 (CSRF) 是一种攻击，恶意站点发送请求到易受攻击的站点在当前登录用户。</span><span class="sxs-lookup"><span data-stu-id="d9355-178">Cross-Site Request Forgery (CSRF) is an attack where a malicious site sends a request to a vulnerable site where the user is currently logged in.</span></span> <span data-ttu-id="d9355-179">SignalR 防止 CSRF 通过使恶意站点来创建有效的请求的 SignalR 应用程序的情况极其少见。</span><span class="sxs-lookup"><span data-stu-id="d9355-179">SignalR prevents CSRF by making it extremely unlikely for a malicious site to create a valid request for your SignalR application.</span></span>

### <a name="description-of-csrf-attack"></a><span data-ttu-id="d9355-180">CSRF 攻击的说明</span><span class="sxs-lookup"><span data-stu-id="d9355-180">Description of CSRF attack</span></span>

<span data-ttu-id="d9355-181">下面是 CSRF 攻击的一个例子：</span><span class="sxs-lookup"><span data-stu-id="d9355-181">Here is an example of a CSRF attack:</span></span>

1. <span data-ttu-id="d9355-182">用户登录到 www.example.com 时，使用窗体身份验证。</span><span class="sxs-lookup"><span data-stu-id="d9355-182">A user logs into www.example.com, using forms authentication.</span></span>
2. <span data-ttu-id="d9355-183">服务器对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="d9355-183">The server authenticates the user.</span></span> <span data-ttu-id="d9355-184">从服务器响应包括身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="d9355-184">The response from the server includes an authentication cookie.</span></span>
3. <span data-ttu-id="d9355-185">而无需注销，用户访问恶意网站。</span><span class="sxs-lookup"><span data-stu-id="d9355-185">Without logging out, the user visits a malicious web site.</span></span> <span data-ttu-id="d9355-186">此恶意站点包含以下 HTML 窗体：</span><span class="sxs-lookup"><span data-stu-id="d9355-186">This malicious site contains the following HTML form:</span></span>

    [!code-html[Main](introduction-to-security/samples/sample1.html)]

   <span data-ttu-id="d9355-187">请注意，窗体操作发到易受攻击的站点，不到的恶意站点。</span><span class="sxs-lookup"><span data-stu-id="d9355-187">Notice that the form action posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="d9355-188">这是 CSRF 的"跨站点"部分。</span><span class="sxs-lookup"><span data-stu-id="d9355-188">This is the "cross-site" part of CSRF.</span></span>
4. <span data-ttu-id="d9355-189">用户单击提交按钮。</span><span class="sxs-lookup"><span data-stu-id="d9355-189">The user clicks the submit button.</span></span> <span data-ttu-id="d9355-190">在浏览器包括与请求的身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="d9355-190">The browser includes the authentication cookie with the request.</span></span>
5. <span data-ttu-id="d9355-191">请求与用户的身份验证上下文，在 example.com 服务器上运行，并可以执行任何操作允许身份验证的用户执行的操作。</span><span class="sxs-lookup"><span data-stu-id="d9355-191">The request runs on the example.com server with the user's authentication context, and can do anything that an authenticated user is allowed to do.</span></span>

<span data-ttu-id="d9355-192">虽然此示例需要用户单击窗体按钮，但恶意页面可以同样轻松地运行 AJAX 请求发送到 SignalR 应用程序的脚本。</span><span class="sxs-lookup"><span data-stu-id="d9355-192">Although this example requires the user to click the form button, the malicious page could just as easily run a script that sends an AJAX request to your SignalR application.</span></span> <span data-ttu-id="d9355-193">此外，使用 SSL 不会阻止实施 CSRF 攻击，因为恶意站点可以发送"https://"请求。</span><span class="sxs-lookup"><span data-stu-id="d9355-193">Moreover, using SSL does not prevent a CSRF attack, because the malicious site can send an "https://" request.</span></span>

<span data-ttu-id="d9355-194">通常情况下，CSRF 攻击是可能对网站使用 cookie 进行身份验证，因为浏览器向目标网站发送所有相关 cookie。</span><span class="sxs-lookup"><span data-stu-id="d9355-194">Typically, CSRF attacks are possible against web sites that use cookies for authentication, because browsers send all relevant cookies to the destination web site.</span></span> <span data-ttu-id="d9355-195">但是，CSRF 攻击并不局限于利用 cookie。</span><span class="sxs-lookup"><span data-stu-id="d9355-195">However, CSRF attacks are not limited to exploiting cookies.</span></span> <span data-ttu-id="d9355-196">例如，基本和摘要式身份验证也是易受攻击的。</span><span class="sxs-lookup"><span data-stu-id="d9355-196">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="d9355-197">使用基本或摘要式身份验证的用户登录后，浏览器自动发送凭据，直到会话结束。</span><span class="sxs-lookup"><span data-stu-id="d9355-197">After a user logs in with Basic or Digest authentication, the browser automatically sends the credentials until the session ends.</span></span>

### <a name="csrf-mitigations-taken-by-signalr"></a><span data-ttu-id="d9355-198">SignalR 所执行的 CSRF 缓解措施</span><span class="sxs-lookup"><span data-stu-id="d9355-198">CSRF mitigations taken by SignalR</span></span>

<span data-ttu-id="d9355-199">SignalR 将执行以下步骤来阻止恶意站点创建对你的应用程序的有效请求。</span><span class="sxs-lookup"><span data-stu-id="d9355-199">SignalR takes the following steps to prevent a malicious site from creating valid requests to your application.</span></span> <span data-ttu-id="d9355-200">SignalR 将执行这些步骤默认情况下，不需要在代码中执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="d9355-200">SignalR takes these steps by default, you do not need to take any action in your code.</span></span>

- <span data-ttu-id="d9355-201">**禁用跨域请求**SignalR 禁用跨域请求，以防止用户从外部域中调用 SignalR 终结点。</span><span class="sxs-lookup"><span data-stu-id="d9355-201">**Disable cross domain requests** SignalR disables cross domain requests to prevent users from calling a SignalR endpoint from an external domain.</span></span> <span data-ttu-id="d9355-202">SignalR 会考虑来自外部域的任何请求无效，并阻止请求。</span><span class="sxs-lookup"><span data-stu-id="d9355-202">SignalR considers any request from an external domain to be invalid and blocks the request.</span></span> <span data-ttu-id="d9355-203">我们建议您保留此默认行为;否则，恶意站点无法欺骗用户在将命令发送到你的站点。</span><span class="sxs-lookup"><span data-stu-id="d9355-203">We recommend that you keep this default behavior; otherwise, a malicious site could trick users into sending commands to your site.</span></span> <span data-ttu-id="d9355-204">如果您需要使用跨域请求，请参阅[如何建立跨域连接](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain)。</span><span class="sxs-lookup"><span data-stu-id="d9355-204">If you need to use cross domain requests, see     [How to establish a cross-domain connection](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain) .</span></span>
- <span data-ttu-id="d9355-205">**将连接标记传递查询字符串，不 cookie 中**SignalR 将连接标记为查询字符串值，而不是传递作为 cookie。</span><span class="sxs-lookup"><span data-stu-id="d9355-205">**Pass connection token in query string, not cookie** SignalR passes the connection token as a query string value, instead of as a cookie.</span></span> <span data-ttu-id="d9355-206">在 cookie 中存储的连接令牌是不安全，因为遇到恶意代码时，浏览器会无意中可以转发的连接令牌。</span><span class="sxs-lookup"><span data-stu-id="d9355-206">Storing the connection token in a cookie is unsafe because the browser can inadvertently forward the connection token when malicious code is encountered.</span></span> <span data-ttu-id="d9355-207">此外，查询字符串中传递的连接令牌从超出当前连接保持阻止的连接令牌。</span><span class="sxs-lookup"><span data-stu-id="d9355-207">Also, passing the connection token in the query string prevents the connection token from persisting beyond the current connection.</span></span> <span data-ttu-id="d9355-208">因此，恶意用户不能在另一个用户的身份验证凭据的请求。</span><span class="sxs-lookup"><span data-stu-id="d9355-208">Therefore, a malicious user cannot make a request under another user's authentication credentials.</span></span>
- <span data-ttu-id="d9355-209">**验证连接令牌**中所述[连接令牌](#connectiontoken)部分中，服务器就知道哪个连接 id 是与每个经过身份验证的用户相关联。</span><span class="sxs-lookup"><span data-stu-id="d9355-209">**Verify connection token** As described in the     [Connection token](#connectiontoken) section, the server knows which connection id is associated with each authenticated user.</span></span> <span data-ttu-id="d9355-210">服务器不会处理来自用户名称不匹配的连接 id 的任何请求。</span><span class="sxs-lookup"><span data-stu-id="d9355-210">The server does not process any request from a connection id that does not match the user name.</span></span> <span data-ttu-id="d9355-211">不太可能因为恶意用户需要知道用户名称和当前的随机生成连接 id，恶意用户无法猜出有效的请求。只要该连接结束，该连接 id 将变为无效。</span><span class="sxs-lookup"><span data-stu-id="d9355-211">It is unlikely a malicious user could guess a valid request because the malicious user would have to know the user name and the current randomly-generated connection id. That connection id becomes invalid as soon as the connection is ended.</span></span> <span data-ttu-id="d9355-212">匿名用户不应有权访问任何敏感信息。</span><span class="sxs-lookup"><span data-stu-id="d9355-212">Anonymous users should not have access to any sensitive information.</span></span>

<a id="recommendations"></a>

## <a name="signalr-security-recommendations"></a><span data-ttu-id="d9355-213">SignalR 的安全建议</span><span class="sxs-lookup"><span data-stu-id="d9355-213">SignalR Security Recommendations</span></span>

<a id="ssl"></a>

### <a name="secure-socket-layers-ssl-protocol"></a><span data-ttu-id="d9355-214">安全套接字层 (SSL) 协议</span><span class="sxs-lookup"><span data-stu-id="d9355-214">Secure Socket Layers (SSL) protocol</span></span>

<span data-ttu-id="d9355-215">SSL 协议使用加密来保护客户端和服务器之间的数据传输。</span><span class="sxs-lookup"><span data-stu-id="d9355-215">The SSL protocol uses encryption to secure the transport of data between a client and server.</span></span> <span data-ttu-id="d9355-216">如果你的 SignalR 应用程序之间传输敏感信息的客户端和服务器，使用 SSL 进行传输。</span><span class="sxs-lookup"><span data-stu-id="d9355-216">If your SignalR application transmits sensitive information between the client and server, use SSL for the transport.</span></span> <span data-ttu-id="d9355-217">有关设置 SSL 的详细信息，请参阅[如何设置 IIS 7 上的 SSL](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="d9355-217">For more information about setting up SSL, see [How to set up SSL on IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).</span></span>

<a id="groupsecurity"></a>

### <a name="do-not-use-groups-as-a-security-mechanism"></a><span data-ttu-id="d9355-218">不将组用作一种安全机制</span><span class="sxs-lookup"><span data-stu-id="d9355-218">Do not use groups as a security mechanism</span></span>

<span data-ttu-id="d9355-219">组是方便的收集相关的用户，但它们不是一个安全机制用于限制对敏感信息的访问。</span><span class="sxs-lookup"><span data-stu-id="d9355-219">Groups are a convenient way of collecting related users, but they are not a secure mechanism for limiting access to sensitive information.</span></span> <span data-ttu-id="d9355-220">当用户可以自动重新加入组重新连接期间，也是如此。</span><span class="sxs-lookup"><span data-stu-id="d9355-220">This is especially true when users can automatically rejoin groups during a reconnect.</span></span> <span data-ttu-id="d9355-221">相反，请考虑向角色添加权限的用户，并限制对该角色的成员的集线器方法的访问。</span><span class="sxs-lookup"><span data-stu-id="d9355-221">Instead, consider adding privileged users to a role and limiting access to a hub method to only members of that role.</span></span> <span data-ttu-id="d9355-222">有关基于角色限制访问的示例，请参阅[身份验证和授权 SignalR 集线器](hub-authorization.md)。</span><span class="sxs-lookup"><span data-stu-id="d9355-222">For an example of restricting access based on a role, see [Authentication and Authorization for SignalR Hubs](hub-authorization.md).</span></span> <span data-ttu-id="d9355-223">检查用户访问组重新连接时的示例，请参阅[使用组](../guide-to-the-api/working-with-groups.md)。</span><span class="sxs-lookup"><span data-stu-id="d9355-223">For an example of checking user access to groups when reconnecting, see [Working with groups](../guide-to-the-api/working-with-groups.md).</span></span>

<a id="input"></a>

### <a name="safely-handling-input-from-clients"></a><span data-ttu-id="d9355-224">安全地处理来自客户端的输入</span><span class="sxs-lookup"><span data-stu-id="d9355-224">Safely handling input from clients</span></span>

<span data-ttu-id="d9355-225">若要确保恶意用户不向其他用户发送脚本，必须对从客户端适用于广播到其他客户端的所有输入进行都编码。</span><span class="sxs-lookup"><span data-stu-id="d9355-225">To ensure that a malicious user does not send script to other users, you must encode all input from clients that is intended for broadcast to other clients.</span></span> <span data-ttu-id="d9355-226">因为 SignalR 应用程序可能有许多不同类型的客户端，应该对接收的客户端，而在服务器上的消息进行编码。</span><span class="sxs-lookup"><span data-stu-id="d9355-226">You should encode messages on the receiving clients rather than the server, because your SignalR application may have many different types of clients.</span></span> <span data-ttu-id="d9355-227">因此，HTML 编码的工作有关 web 客户端，而不是其他类型的客户端。</span><span class="sxs-lookup"><span data-stu-id="d9355-227">Therefore, HTML-encoding works for a web client, but not for other types of clients.</span></span> <span data-ttu-id="d9355-228">例如，若要显示的聊天消息的 web 客户端方法将安全地处理用户名称和消息通过调用`html()`函数。</span><span class="sxs-lookup"><span data-stu-id="d9355-228">For example, a web client method to display a chat message would safely handle the user name and message by calling the `html()` function.</span></span>

[!code-html[Main](introduction-to-security/samples/sample2.html?highlight=3-4)]

<a id="reconcile"></a>

### <a name="reconciling-a-change-in-user-status-with-an-active-connection"></a><span data-ttu-id="d9355-229">协调与活动连接的用户状态中的更改</span><span class="sxs-lookup"><span data-stu-id="d9355-229">Reconciling a change in user status with an active connection</span></span>

<span data-ttu-id="d9355-230">如果用户的身份验证状态发生更改的活动连接存在时，用户将收到一条指出错误、"用户标识不能更改活动的 SignalR 连接期间。"</span><span class="sxs-lookup"><span data-stu-id="d9355-230">If a user's authentication status changes while an active connection exists, the user will receive an error that states, "The user identity cannot change during an active SignalR connection."</span></span> <span data-ttu-id="d9355-231">在这种情况下，你的应用程序应重新连接到服务器，以确保连接 id 和用户名进行协调。</span><span class="sxs-lookup"><span data-stu-id="d9355-231">In that case, your application should re-connect to the server to make sure the connection id and username are coordinated.</span></span> <span data-ttu-id="d9355-232">例如，如果你的应用程序允许用户注销的活动连接存在时，该连接的用户名将不再匹配下一个请求传入的名称。</span><span class="sxs-lookup"><span data-stu-id="d9355-232">For example, if your application allows the user to log out while an active connection exists, the username for the connection will no longer match the name that is passed in for the next request.</span></span> <span data-ttu-id="d9355-233">将想要在用户注销之前停止连接，然后重新启动它。</span><span class="sxs-lookup"><span data-stu-id="d9355-233">You will want to stop the connection before the user logs out, and then restart it.</span></span>

<span data-ttu-id="d9355-234">但是，务必要注意，大多数应用程序不需要手动停止和启动连接。</span><span class="sxs-lookup"><span data-stu-id="d9355-234">However, it is important to note that most applications will not need to manually stop and start the connection.</span></span> <span data-ttu-id="d9355-235">如果你的应用程序用户重定向到单独的页面后日志记录，如 Web 窗体应用程序或 MVC 应用程序中的默认行为，或者注销后刷新当前页，自动断开连接的活动连接，而不需要执行任何其他操作。</span><span class="sxs-lookup"><span data-stu-id="d9355-235">If your application redirects users to a separate page after logging out, such as the default behavior in a Web Forms application or MVC application, or refreshes the current page after logging out, the active connection is automatically disconnected and does not require any additional action.</span></span>

<span data-ttu-id="d9355-236">下面的示例演示如何停止和启动连接时，用户状态已更改。</span><span class="sxs-lookup"><span data-stu-id="d9355-236">The following example shows how to stop and start a connection when the user status has changed.</span></span>

[!code-html[Main](introduction-to-security/samples/sample3.html)]

<span data-ttu-id="d9355-237">或者，如果您的网站通过窗体身份验证，使用可调到期，并且没有任何活动保持有效的身份验证 cookie，可能会更改用户的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="d9355-237">Or, the user's authentication status may change if your site uses sliding expiration with Forms Authentication, and there is no activity to keep the authentication cookie valid.</span></span> <span data-ttu-id="d9355-238">在这种情况下，将注销用户和用户名称将不再匹配的连接令牌中的用户名称。</span><span class="sxs-lookup"><span data-stu-id="d9355-238">In that case, the user will be logged out and the user name will no longer match the user name in the connection token.</span></span> <span data-ttu-id="d9355-239">可以通过添加一些定期请求的资源，在 web 服务器以保持身份验证 cookie 有效的脚本来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="d9355-239">You can fix this problem by adding some script that periodically requests a resource on the web server to keep the authentication cookie valid.</span></span> <span data-ttu-id="d9355-240">下面的示例演示如何请求每隔 30 分钟的资源。</span><span class="sxs-lookup"><span data-stu-id="d9355-240">The following example shows how to request a resource every 30 minutes.</span></span>

[!code-javascript[Main](introduction-to-security/samples/sample4.js)]

<a id="autogen"></a>

### <a name="automatically-generated-javascript-proxy-files"></a><span data-ttu-id="d9355-241">自动生成的 JavaScript 代理文件</span><span class="sxs-lookup"><span data-stu-id="d9355-241">Automatically generated JavaScript proxy files</span></span>

<span data-ttu-id="d9355-242">如果您不想要在每个用户的 JavaScript 代理文件中包含所有集线器和方法，可以禁用自动生成相应的文件。</span><span class="sxs-lookup"><span data-stu-id="d9355-242">If you do not want to include all of the hubs and methods in the JavaScript proxy file for each user, you can disable the automatic generation of the file.</span></span> <span data-ttu-id="d9355-243">如果您具有多个中心和方法，但不是希望每个用户需要注意的所有方法，可以选择此选项。</span><span class="sxs-lookup"><span data-stu-id="d9355-243">You might choose this option if you have multiple hubs and methods, but do not want every user to be aware of all of the methods.</span></span> <span data-ttu-id="d9355-244">通过设置禁用自动生成**EnableJavaScriptProxies**到**false**。</span><span class="sxs-lookup"><span data-stu-id="d9355-244">You disable automatic generation by setting **EnableJavaScriptProxies** to **false**.</span></span>

[!code-csharp[Main](introduction-to-security/samples/sample5.cs)]

<span data-ttu-id="d9355-245">有关 JavaScript 代理文件的详细信息，请参阅[生成的代理和它为您完成](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy)。</span><span class="sxs-lookup"><span data-stu-id="d9355-245">For more information about the JavaScript proxy files, see [The generated proxy and what it does for you](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).</span></span> <a id="exceptions"></a>

### <a name="exceptions"></a><span data-ttu-id="d9355-246">Exceptions</span><span class="sxs-lookup"><span data-stu-id="d9355-246">Exceptions</span></span>

<span data-ttu-id="d9355-247">应避免将异常对象传递到客户端，因为对象可能会公开给客户端的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="d9355-247">You should avoid passing exception objects to clients because the objects may expose sensitive information to the clients.</span></span> <span data-ttu-id="d9355-248">相反，在显示的相关错误消息的客户端上调用方法。</span><span class="sxs-lookup"><span data-stu-id="d9355-248">Instead, call a method on the client that displays the relevant error message.</span></span>

[!code-csharp[Main](introduction-to-security/samples/sample6.cs)]
