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
<a name="introduction-to-signalr-security-signalr-1x"></a><span data-ttu-id="418be-103">SignalR 安全性简介 (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="418be-103">Introduction to SignalR Security (SignalR 1.x)</span></span>
====================
<span data-ttu-id="418be-104">通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="418be-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="418be-105">本文介绍开发 SignalR 应用程序时必须考虑的安全问题。</span><span class="sxs-lookup"><span data-stu-id="418be-105">This article describes the security issues you must consider when developing a SignalR application.</span></span>


## <a name="overview"></a><span data-ttu-id="418be-106">概述</span><span class="sxs-lookup"><span data-stu-id="418be-106">Overview</span></span>

<span data-ttu-id="418be-107">本文档包含以下各节：</span><span class="sxs-lookup"><span data-stu-id="418be-107">This document contains the following sections:</span></span>

- [<span data-ttu-id="418be-108">SignalR 安全性概念</span><span class="sxs-lookup"><span data-stu-id="418be-108">SignalR Security Concepts</span></span>](#concepts)

    - [<span data-ttu-id="418be-109">身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="418be-109">Authentication and authorization</span></span>](#authentication)
    - [<span data-ttu-id="418be-110">连接令牌</span><span class="sxs-lookup"><span data-stu-id="418be-110">Connection token</span></span>](#connectiontoken)
    - [<span data-ttu-id="418be-111">重新连接时重新加入组</span><span class="sxs-lookup"><span data-stu-id="418be-111">Rejoining groups when reconnecting</span></span>](#rejoingroup)
- [<span data-ttu-id="418be-112">SignalR 是如何阻止跨站点请求伪造</span><span class="sxs-lookup"><span data-stu-id="418be-112">How SignalR prevents Cross-Site Request Forgery</span></span>](#csrf)
- [<span data-ttu-id="418be-113">SignalR 的安全建议</span><span class="sxs-lookup"><span data-stu-id="418be-113">SignalR Security Recommendations</span></span>](#recommendations)

    - [<span data-ttu-id="418be-114">安全套接字层 (SSL) 协议</span><span class="sxs-lookup"><span data-stu-id="418be-114">Secure Socket Layers (SSL) protocol</span></span>](#ssl)
    - [<span data-ttu-id="418be-115">不将组用作一种安全机制</span><span class="sxs-lookup"><span data-stu-id="418be-115">Do not use groups as a security mechanism</span></span>](#groupsecurity)
    - [<span data-ttu-id="418be-116">安全地处理来自客户端的输入</span><span class="sxs-lookup"><span data-stu-id="418be-116">Safely handling input from clients</span></span>](#input)
    - [<span data-ttu-id="418be-117">协调与活动连接的用户状态中的更改</span><span class="sxs-lookup"><span data-stu-id="418be-117">Reconciling a change in user status with an active connection</span></span>](#reconcile)
    - [<span data-ttu-id="418be-118">自动生成的 JavaScript 代理文件</span><span class="sxs-lookup"><span data-stu-id="418be-118">Automatically generated JavaScript proxy files</span></span>](#autogen)
    - [<span data-ttu-id="418be-119">异常</span><span class="sxs-lookup"><span data-stu-id="418be-119">Exceptions</span></span>](#exceptions)

<a id="concepts"></a>

## <a name="signalr-security-concepts"></a><span data-ttu-id="418be-120">SignalR 安全性概念</span><span class="sxs-lookup"><span data-stu-id="418be-120">SignalR Security Concepts</span></span>

<a id="authentication"></a>

### <a name="authentication-and-authorization"></a><span data-ttu-id="418be-121">身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="418be-121">Authentication and authorization</span></span>

<span data-ttu-id="418be-122">SignalR 设计为可集成到现有应用程序的身份验证结构。</span><span class="sxs-lookup"><span data-stu-id="418be-122">SignalR is designed to be integrated into the existing authentication structure for an application.</span></span> <span data-ttu-id="418be-123">它不提供任何功能的用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="418be-123">It does not provide any features for authenticating users.</span></span> <span data-ttu-id="418be-124">相反，您进行身份验证的用户通常会在应用程序中，然后使用 SignalR 代码中的身份验证的结果。</span><span class="sxs-lookup"><span data-stu-id="418be-124">Instead, you authenticate users as you would normally in your application, and then work with the results of the authentication in your SignalR code.</span></span> <span data-ttu-id="418be-125">例如，可能会使用 ASP.NET 窗体身份验证，对用户身份验证，然后在中心，会强制哪些用户或角色有权调用的方法。</span><span class="sxs-lookup"><span data-stu-id="418be-125">For example, you might authenticate your users with ASP.NET forms authentication, and then in your hub, enforce which users or roles are authorized to call a method.</span></span> <span data-ttu-id="418be-126">在中心，也可以传递身份验证信息，如用户名或用户是否属于一个角色，向客户端。</span><span class="sxs-lookup"><span data-stu-id="418be-126">In your hub, you can also pass authentication information, such as user name or whether a user belongs to a role, to the client.</span></span>

<span data-ttu-id="418be-127">提供 SignalR [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx)属性来指定哪些用户有权访问的中心或方法。</span><span class="sxs-lookup"><span data-stu-id="418be-127">SignalR provides the [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) attribute to specify which users have access to a hub or method.</span></span> <span data-ttu-id="418be-128">将 Authorize 属性应用到一个中心或中心中的特定方法。</span><span class="sxs-lookup"><span data-stu-id="418be-128">You apply the Authorize attribute to either a hub or particular methods in a hub.</span></span> <span data-ttu-id="418be-129">如果不使用 Authorize 属性，中心上的所有公共方法可供连接到集线器的客户端。</span><span class="sxs-lookup"><span data-stu-id="418be-129">Without the Authorize attribute, all public methods on the hub are available to a client that is connected to the hub.</span></span> <span data-ttu-id="418be-130">有关中心的详细信息，请参阅[身份验证和授权 SignalR 集线器](../security/hub-authorization.md)。</span><span class="sxs-lookup"><span data-stu-id="418be-130">For more information about hubs, see [Authentication and Authorization for SignalR Hubs](../security/hub-authorization.md).</span></span>

<span data-ttu-id="418be-131">`Authorize`属性仅用于中心。</span><span class="sxs-lookup"><span data-stu-id="418be-131">The `Authorize` attribute is used only with hubs.</span></span> <span data-ttu-id="418be-132">若要使用时强制实施授权规则`PersistentConnection`必须重写`AuthorizeRequest`方法。</span><span class="sxs-lookup"><span data-stu-id="418be-132">To enforce authorization rules when using a `PersistentConnection` you must override the `AuthorizeRequest` method.</span></span> <span data-ttu-id="418be-133">有关持久连接的详细信息，请参阅[身份验证和授权 SignalR 永久性连接](../security/persistent-connection-authorization.md)。</span><span class="sxs-lookup"><span data-stu-id="418be-133">For more information about persistent connections, see [Authentication and Authorization for SignalR Persistent Connections](../security/persistent-connection-authorization.md).</span></span>

<a id="connectiontoken"></a>

### <a name="connection-token"></a><span data-ttu-id="418be-134">连接令牌</span><span class="sxs-lookup"><span data-stu-id="418be-134">Connection token</span></span>

<span data-ttu-id="418be-135">SignalR 应执行恶意命令验证发件人标识的风险。</span><span class="sxs-lookup"><span data-stu-id="418be-135">SignalR mitigates the risk of executing malicious commands by validating the identity of the sender.</span></span> <span data-ttu-id="418be-136">包含的连接 id 和身份验证的用户的用户名的连接令牌为每个请求客户端和服务器之间传递。</span><span class="sxs-lookup"><span data-stu-id="418be-136">A connection token, containing the connection id and username for authenticated users, is passed between the client and server for each request.</span></span> <span data-ttu-id="418be-137">连接 id 是随机生成服务器时的新连接创建，并且连接的持续时间内保持不变的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="418be-137">The connection id is a unique identifier that is randomly generated by the server when a new connection is created, and is persisted for the duration of the connection.</span></span> <span data-ttu-id="418be-138">由 web 应用程序的身份验证机制提供用户名。</span><span class="sxs-lookup"><span data-stu-id="418be-138">The username is provided by the authentication mechanism for the web application.</span></span> <span data-ttu-id="418be-139">加密和数字签名进行保护的连接令牌。</span><span class="sxs-lookup"><span data-stu-id="418be-139">The connection token is protected with encryption and a digital signature.</span></span>

![](introduction-to-security/_static/image2.png)

<span data-ttu-id="418be-140">对于每个请求，服务器来验证该令牌，确保该请求来自指定的用户的内容。</span><span class="sxs-lookup"><span data-stu-id="418be-140">For each request, the server validates the contents of the token to ensure that the request is coming from the specified user.</span></span> <span data-ttu-id="418be-141">用户名必须对应于连接 id。通过验证的连接 id 和用户名，SignalR 会阻止恶意用户轻松地模拟另一个用户。</span><span class="sxs-lookup"><span data-stu-id="418be-141">The username must correspond to the connection id. By validating both the connection id and the username, SignalR prevents a malicious user from easily impersonating another user.</span></span> <span data-ttu-id="418be-142">如果服务器无法验证的连接令牌，则请求将失败。</span><span class="sxs-lookup"><span data-stu-id="418be-142">If the server cannot validate the connection token, the request fails.</span></span>

![](introduction-to-security/_static/image4.png)

<span data-ttu-id="418be-143">连接 id 是验证过程的一部分，因为不应显示一个用户的连接 id 向其他用户或在客户端上的值如存储在一个 cookie。</span><span class="sxs-lookup"><span data-stu-id="418be-143">Because the connection id is part of the verification process, you should not reveal one user's connection id to other users or store the value on the client, such as in a cookie.</span></span>

<a id="rejoingroup"></a>

### <a name="rejoining-groups-when-reconnecting"></a><span data-ttu-id="418be-144">重新连接时重新加入组</span><span class="sxs-lookup"><span data-stu-id="418be-144">Rejoining groups when reconnecting</span></span>

<span data-ttu-id="418be-145">默认情况下，SignalR 应用程序将自动重新将用户分配到相应的组的临时中断后，如删除并重新建立了连接超时之前连接重新连接时。当重新连接后，客户端传递包括连接 id 和分配的组的组令牌。</span><span class="sxs-lookup"><span data-stu-id="418be-145">By default, the SignalR application will automatically re-assign a user to the appropriate groups when reconnecting from a temporary disruption, such as when a connection is dropped and re-established before the connection times out. When reconnecting, the client passes a group token that includes the connection id and the assigned groups.</span></span> <span data-ttu-id="418be-146">组令牌进行数字签名和加密。</span><span class="sxs-lookup"><span data-stu-id="418be-146">The group token is digitally signed and encrypted.</span></span> <span data-ttu-id="418be-147">客户端后重新连接; 保留相同的连接 id因此，重新连接客户端传递的连接 id 必须匹配上一个客户端使用的连接 id。</span><span class="sxs-lookup"><span data-stu-id="418be-147">The client retains the same connection id after a reconnection; therefore, the connection id passed from the reconnected client must match the previous connection id used by the client.</span></span> <span data-ttu-id="418be-148">此验证会阻止恶意用户将请求加入未经授权的组重新连接时传递。</span><span class="sxs-lookup"><span data-stu-id="418be-148">This verification prevents a malicious user from passing requests to join unauthorized groups when reconnecting.</span></span>

<span data-ttu-id="418be-149">但是，务必要注意，组令牌不会过期。</span><span class="sxs-lookup"><span data-stu-id="418be-149">However, it is important to note, that the group token does not expire.</span></span> <span data-ttu-id="418be-150">如果用户在过去，属于某个组，但已阻止从该组，该用户可能能够模拟包含被禁止的组的组令牌。</span><span class="sxs-lookup"><span data-stu-id="418be-150">If a user belonged to a group in the past, but was banned from that group, that user may be able to mimic a group token that includes the prohibited group.</span></span> <span data-ttu-id="418be-151">如果您需要安全地管理哪些用户属于哪些组，您需要将该数据的服务器上，如存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="418be-151">If you need to securely manage which users belong to which groups, you need to store that data on the server, such as in a database.</span></span> <span data-ttu-id="418be-152">然后，将逻辑添加到你的应用程序，用于在服务器上验证用户是否属于一个组。</span><span class="sxs-lookup"><span data-stu-id="418be-152">Then, add logic to your application that verifies on the server whether a user belongs to a group.</span></span> <span data-ttu-id="418be-153">验证组成员身份的示例，请参阅[使用组](../guide-to-the-api/working-with-groups.md)。</span><span class="sxs-lookup"><span data-stu-id="418be-153">For an example of verifying group membership, see [Working with groups](../guide-to-the-api/working-with-groups.md).</span></span>

<span data-ttu-id="418be-154">自动重新加入组仅适用于连接暂时中断后重新连接。</span><span class="sxs-lookup"><span data-stu-id="418be-154">Automatically rejoining groups only applies when a connection is reconnected after a temporary disruption.</span></span> <span data-ttu-id="418be-155">如果用户断开连接通过导航离开该应用程序或应用程序重新启动，你的应用程序必须处理如何将该用户添加到正确的组。</span><span class="sxs-lookup"><span data-stu-id="418be-155">If a user disconnects by navigating away from the application or the application restarts, your application must handle how to add that user to the correct groups.</span></span> <span data-ttu-id="418be-156">有关详细信息，请参阅[使用组](../guide-to-the-api/working-with-groups.md)。</span><span class="sxs-lookup"><span data-stu-id="418be-156">For more information, see [Working with groups](../guide-to-the-api/working-with-groups.md).</span></span>

<a id="csrf"></a>

## <a name="how-signalr-prevents-cross-site-request-forgery"></a><span data-ttu-id="418be-157">SignalR 是如何阻止跨站点请求伪造</span><span class="sxs-lookup"><span data-stu-id="418be-157">How SignalR prevents Cross-Site Request Forgery</span></span>

<span data-ttu-id="418be-158">跨站点请求伪造 (CSRF) 是一种攻击，恶意站点发送请求到易受攻击的站点在当前登录用户。</span><span class="sxs-lookup"><span data-stu-id="418be-158">Cross-Site Request Forgery (CSRF) is an attack where a malicious site sends a request to a vulnerable site where the user is currently logged in.</span></span> <span data-ttu-id="418be-159">SignalR 防止 CSRF 通过使恶意站点来创建有效的请求的 SignalR 应用程序的情况极其少见。</span><span class="sxs-lookup"><span data-stu-id="418be-159">SignalR prevents CSRF by making it extremely unlikely for a malicious site to create a valid request for your SignalR application.</span></span>

### <a name="description-of-csrf-attack"></a><span data-ttu-id="418be-160">CSRF 攻击的说明</span><span class="sxs-lookup"><span data-stu-id="418be-160">Description of CSRF attack</span></span>

<span data-ttu-id="418be-161">下面是 CSRF 攻击的一个例子：</span><span class="sxs-lookup"><span data-stu-id="418be-161">Here is an example of a CSRF attack:</span></span>

1. <span data-ttu-id="418be-162">用户登录到`www.example.com`，使用窗体身份验证。</span><span class="sxs-lookup"><span data-stu-id="418be-162">A user logs into `www.example.com`, using forms authentication.</span></span>
2. <span data-ttu-id="418be-163">服务器对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="418be-163">The server authenticates the user.</span></span> <span data-ttu-id="418be-164">从服务器响应包括身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="418be-164">The response from the server includes an authentication cookie.</span></span>
3. <span data-ttu-id="418be-165">而无需注销，用户访问恶意网站。</span><span class="sxs-lookup"><span data-stu-id="418be-165">Without logging out, the user visits a malicious web site.</span></span> <span data-ttu-id="418be-166">此恶意站点包含以下 HTML 窗体：</span><span class="sxs-lookup"><span data-stu-id="418be-166">This malicious site contains the following HTML form:</span></span> 

    [!code-html[Main](introduction-to-security/samples/sample1.html)]

   <span data-ttu-id="418be-167">请注意，窗体操作发到易受攻击的站点，不到的恶意站点。</span><span class="sxs-lookup"><span data-stu-id="418be-167">Notice that the form action posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="418be-168">这是 CSRF 的"跨站点"部分。</span><span class="sxs-lookup"><span data-stu-id="418be-168">This is the "cross-site" part of CSRF.</span></span>
4. <span data-ttu-id="418be-169">用户单击提交按钮。</span><span class="sxs-lookup"><span data-stu-id="418be-169">The user clicks the submit button.</span></span> <span data-ttu-id="418be-170">在浏览器包括与请求的身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="418be-170">The browser includes the authentication cookie with the request.</span></span>
5. <span data-ttu-id="418be-171">请求与用户的身份验证上下文，在 example.com 服务器上运行，并可以执行任何操作允许身份验证的用户执行的操作。</span><span class="sxs-lookup"><span data-stu-id="418be-171">The request runs on the example.com server with the user's authentication context, and can do anything that an authenticated user is allowed to do.</span></span>

<span data-ttu-id="418be-172">虽然此示例需要用户单击窗体按钮，但恶意页面可以同样轻松地运行 AJAX 请求发送到 SignalR 应用程序的脚本。</span><span class="sxs-lookup"><span data-stu-id="418be-172">Although this example requires the user to click the form button, the malicious page could just as easily run a script that sends an AJAX request to your SignalR application.</span></span> <span data-ttu-id="418be-173">此外，使用 SSL 不会阻止实施 CSRF 攻击，因为恶意站点可以发送"https://"请求。</span><span class="sxs-lookup"><span data-stu-id="418be-173">Moreover, using SSL does not prevent a CSRF attack, because the malicious site can send an "https://" request.</span></span>

<span data-ttu-id="418be-174">通常情况下，CSRF 攻击是可能对网站使用 cookie 进行身份验证，因为浏览器向目标网站发送所有相关 cookie。</span><span class="sxs-lookup"><span data-stu-id="418be-174">Typically, CSRF attacks are possible against web sites that use cookies for authentication, because browsers send all relevant cookies to the destination web site.</span></span> <span data-ttu-id="418be-175">但是，CSRF 攻击并不局限于利用 cookie。</span><span class="sxs-lookup"><span data-stu-id="418be-175">However, CSRF attacks are not limited to exploiting cookies.</span></span> <span data-ttu-id="418be-176">例如，基本和摘要式身份验证也是易受攻击的。</span><span class="sxs-lookup"><span data-stu-id="418be-176">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="418be-177">使用基本或摘要式身份验证的用户登录后，浏览器自动发送凭据，直到会话结束。</span><span class="sxs-lookup"><span data-stu-id="418be-177">After a user logs in with Basic or Digest authentication, the browser automatically sends the credentials until the session ends.</span></span>

### <a name="csrf-mitigations-taken-by-signalr"></a><span data-ttu-id="418be-178">SignalR 所执行的 CSRF 缓解措施</span><span class="sxs-lookup"><span data-stu-id="418be-178">CSRF mitigations taken by SignalR</span></span>

<span data-ttu-id="418be-179">SignalR 将执行以下步骤来阻止恶意站点创建到 SignalR 应用程序的有效请求。</span><span class="sxs-lookup"><span data-stu-id="418be-179">SignalR takes the following steps to prevent a malicious site from creating valid requests to your SignalR application.</span></span> <span data-ttu-id="418be-180">默认情况下执行这些步骤，并不需要在代码中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="418be-180">These steps are taken by default and do not require any action in your code.</span></span>

- <span data-ttu-id="418be-181">**禁用跨域请求**</span><span class="sxs-lookup"><span data-stu-id="418be-181">**Disable cross domain requests**</span></span>  
 <span data-ttu-id="418be-182">默认情况下，跨域请求中将禁用 SignalR 应用程序以防止用户从外部域中调用 SignalR 终结点。</span><span class="sxs-lookup"><span data-stu-id="418be-182">By default, cross domain requests are disabled in a SignalR application to prevent users from calling a SignalR endpoint from an external domain.</span></span> <span data-ttu-id="418be-183">来自外部域的任何请求将自动被视为无效并被阻止。</span><span class="sxs-lookup"><span data-stu-id="418be-183">Any request that comes from an external domain is automatically considered invalid and is blocked.</span></span> <span data-ttu-id="418be-184">建议保留此默认行为;否则，恶意站点无法欺骗用户在将命令发送到你的站点。</span><span class="sxs-lookup"><span data-stu-id="418be-184">It is recommended that you keep this default behavior; otherwise, a malicious site could trick users into sending commands to your site.</span></span> <span data-ttu-id="418be-185">如果您需要使用跨域请求，请参阅[如何建立跨域连接](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain)。</span><span class="sxs-lookup"><span data-stu-id="418be-185">If you need to use cross domain requests, see     [How to establish a cross-domain connection](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain) .</span></span>
- <span data-ttu-id="418be-186">**传递查询字符串，不 cookie 中的连接令牌**</span><span class="sxs-lookup"><span data-stu-id="418be-186">**Pass connection token in query string, not cookie**</span></span>  
 <span data-ttu-id="418be-187">SignalR 传递的连接作为查询字符串值，而不是作为 cookie。</span><span class="sxs-lookup"><span data-stu-id="418be-187">SignalR passes the connection token as a query string value, instead of as a cookie.</span></span> <span data-ttu-id="418be-188">通过不存储的连接令牌作为 cookie 的连接令牌不会无意中转发由浏览器时遇到恶意代码。</span><span class="sxs-lookup"><span data-stu-id="418be-188">By not storing the connection token as a cookie, the connection token is not inadvertently forwarded by the browser when malicious code is encountered.</span></span> <span data-ttu-id="418be-189">此外，不会超出当前连接保留的连接令牌。</span><span class="sxs-lookup"><span data-stu-id="418be-189">Also, the connection token is not persisted beyond the current connection.</span></span> <span data-ttu-id="418be-190">因此，恶意用户不能在另一个用户的身份验证凭据的请求。</span><span class="sxs-lookup"><span data-stu-id="418be-190">Therefore, a malicious user cannot make a request under another user's authentication credentials.</span></span>
- <span data-ttu-id="418be-191">**验证连接令牌**</span><span class="sxs-lookup"><span data-stu-id="418be-191">**Verify connection token**</span></span>  
 <span data-ttu-id="418be-192">如中所述[连接令牌](#connectiontoken)部分中，服务器就知道哪个连接 id 是与每个经过身份验证的用户相关联。</span><span class="sxs-lookup"><span data-stu-id="418be-192">As described in the [Connection token](#connectiontoken) section, the server knows which connection id is associated with each authenticated user.</span></span> <span data-ttu-id="418be-193">服务器不会处理来自用户名称不匹配的连接 id 的任何请求。</span><span class="sxs-lookup"><span data-stu-id="418be-193">The server does not process any request from a connection id that does not match the user name.</span></span> <span data-ttu-id="418be-194">不太可能因为恶意用户需要知道用户名称和当前的随机生成连接 id，恶意用户无法猜出有效的请求。只要该连接结束，该连接 id 将变为无效。</span><span class="sxs-lookup"><span data-stu-id="418be-194">It is unlikely a malicious user could guess a valid request because the malicious user would have to know the user name and the current randomly-generated connection id. That connection id becomes invalid as soon as the connection is ended.</span></span> <span data-ttu-id="418be-195">匿名用户不应有权访问任何敏感信息。</span><span class="sxs-lookup"><span data-stu-id="418be-195">Anonymous users should not have access to any sensitive information.</span></span>

<a id="recommendations"></a>

## <a name="signalr-security-recommendations"></a><span data-ttu-id="418be-196">SignalR 的安全建议</span><span class="sxs-lookup"><span data-stu-id="418be-196">SignalR Security Recommendations</span></span>

<a id="ssl"></a>

### <a name="secure-socket-layers-ssl-protocol"></a><span data-ttu-id="418be-197">安全套接字层 (SSL) 协议</span><span class="sxs-lookup"><span data-stu-id="418be-197">Secure Socket Layers (SSL) protocol</span></span>

<span data-ttu-id="418be-198">SSL 协议使用加密来保护客户端和服务器之间的数据传输。</span><span class="sxs-lookup"><span data-stu-id="418be-198">The SSL protocol uses encryption to secure the transport of data between a client and server.</span></span> <span data-ttu-id="418be-199">如果你的 SignalR 应用程序之间传输敏感信息的客户端和服务器，使用 SSL 进行传输。</span><span class="sxs-lookup"><span data-stu-id="418be-199">If your SignalR application transmits sensitive information between the client and server, use SSL for the transport.</span></span> <span data-ttu-id="418be-200">有关设置 SSL 的详细信息，请参阅[如何设置 IIS 7 上的 SSL](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="418be-200">For more information about setting up SSL, see [How to set up SSL on IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).</span></span>

<a id="groupsecurity"></a>

### <a name="do-not-use-groups-as-a-security-mechanism"></a><span data-ttu-id="418be-201">不将组用作一种安全机制</span><span class="sxs-lookup"><span data-stu-id="418be-201">Do not use groups as a security mechanism</span></span>

<span data-ttu-id="418be-202">组是方便的收集相关的用户，但它们不是一个安全机制用于限制对敏感信息的访问。</span><span class="sxs-lookup"><span data-stu-id="418be-202">Groups are a convenient way of collecting related users, but they are not a secure mechanism for limiting access to sensitive information.</span></span> <span data-ttu-id="418be-203">当用户可以自动重新加入组重新连接期间，也是如此。</span><span class="sxs-lookup"><span data-stu-id="418be-203">This is especially true when users can automatically rejoin groups during a reconnect.</span></span> <span data-ttu-id="418be-204">相反，请考虑向角色添加权限的用户，并限制对该角色的成员的集线器方法的访问。</span><span class="sxs-lookup"><span data-stu-id="418be-204">Instead, consider adding privileged users to a role and limiting access to a hub method to only members of that role.</span></span> <span data-ttu-id="418be-205">有关基于角色限制访问的示例，请参阅[身份验证和授权 SignalR 集线器](../security/hub-authorization.md)。</span><span class="sxs-lookup"><span data-stu-id="418be-205">For an example of restricting access based on a role, see [Authentication and Authorization for SignalR Hubs](../security/hub-authorization.md).</span></span> <span data-ttu-id="418be-206">检查用户访问组重新连接时的示例，请参阅[使用组](../guide-to-the-api/working-with-groups.md)。</span><span class="sxs-lookup"><span data-stu-id="418be-206">For an example of checking user access to groups when reconnecting, see [Working with groups](../guide-to-the-api/working-with-groups.md).</span></span>

<a id="input"></a>

### <a name="safely-handling-input-from-clients"></a><span data-ttu-id="418be-207">安全地处理来自客户端的输入</span><span class="sxs-lookup"><span data-stu-id="418be-207">Safely handling input from clients</span></span>

<span data-ttu-id="418be-208">从客户端适用于广播到其他客户端的所有输入必须进行都编码，以确保恶意用户不向其他用户发送脚本。</span><span class="sxs-lookup"><span data-stu-id="418be-208">All input from clients that is intended for broadcast to other clients must be encoded to ensure that a malicious user does not send script to other users.</span></span> <span data-ttu-id="418be-209">它最好在接收客户端而非在服务器上的消息进行编码，因为 SignalR 应用程序可能具有许多不同类型的客户端。</span><span class="sxs-lookup"><span data-stu-id="418be-209">It is best to encode messages on the receiving clients rather than the server, because your SignalR application may have many different types of clients.</span></span> <span data-ttu-id="418be-210">因此，HTML 编码的工作有关 web 客户端，而不是其他类型的客户端。</span><span class="sxs-lookup"><span data-stu-id="418be-210">Therefore, HTML-encoding works for a web client, but not for other types of clients.</span></span> <span data-ttu-id="418be-211">例如，若要显示的聊天消息的 web 客户端方法将安全地处理用户名称和消息通过调用`html()`函数。</span><span class="sxs-lookup"><span data-stu-id="418be-211">For example, a web client method to display a chat message would safely handle the user name and message by calling the `html()` function.</span></span>

[!code-html[Main](introduction-to-security/samples/sample2.html?highlight=3-4)]

<a id="reconcile"></a>

### <a name="reconciling-a-change-in-user-status-with-an-active-connection"></a><span data-ttu-id="418be-212">协调与活动连接的用户状态中的更改</span><span class="sxs-lookup"><span data-stu-id="418be-212">Reconciling a change in user status with an active connection</span></span>

<span data-ttu-id="418be-213">如果用户的身份验证状态发生更改的活动连接存在时，用户将收到一条指出错误、"用户标识不能更改活动的 SignalR 连接期间。"</span><span class="sxs-lookup"><span data-stu-id="418be-213">If a user's authentication status changes while an active connection exists, the user will receive an error that states, "The user identity cannot change during an active SignalR connection."</span></span> <span data-ttu-id="418be-214">在这种情况下，你的应用程序应重新连接到服务器，以确保连接 id 和用户名进行协调。</span><span class="sxs-lookup"><span data-stu-id="418be-214">In that case, your application should re-connect to the server to make sure the connection id and username are coordinated.</span></span> <span data-ttu-id="418be-215">例如，如果你的应用程序允许用户注销的活动连接存在时，该连接的用户名将不再匹配下一个请求传入的名称。</span><span class="sxs-lookup"><span data-stu-id="418be-215">For example, if your application allows the user to log out while an active connection exists, the username for the connection will no longer match the name that is passed in for the next request.</span></span> <span data-ttu-id="418be-216">将想要在用户注销之前停止连接，然后重新启动它。</span><span class="sxs-lookup"><span data-stu-id="418be-216">You will want to stop the connection before the user logs out, and then restart it.</span></span>

<span data-ttu-id="418be-217">但是，务必要注意，大多数应用程序不需要手动停止和启动连接。</span><span class="sxs-lookup"><span data-stu-id="418be-217">However, it is important to note that most applications will not need to manually stop and start the connection.</span></span> <span data-ttu-id="418be-218">如果你的应用程序用户重定向到单独的页面后日志记录，如 Web 窗体应用程序或 MVC 应用程序中的默认行为，或者注销后刷新当前页，自动断开连接的活动连接，而不需要执行任何其他操作。</span><span class="sxs-lookup"><span data-stu-id="418be-218">If your application redirects users to a separate page after logging out, such as the default behavior in a Web Forms application or MVC application, or refreshes the current page after logging out, the active connection is automatically disconnected and does not require any additional action.</span></span>

<span data-ttu-id="418be-219">下面的示例演示如何停止和启动连接时，用户状态已更改。</span><span class="sxs-lookup"><span data-stu-id="418be-219">The following example shows how to stop and start a connection when the user status has changed.</span></span>

[!code-html[Main](introduction-to-security/samples/sample3.html)]

<span data-ttu-id="418be-220">或者，如果您的网站通过窗体身份验证，使用可调到期，并且没有任何活动保持有效的身份验证 cookie，可能会更改用户的身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="418be-220">Or, the user's authentication status may change if your site uses sliding expiration with Forms Authentication, and there is no activity to keep the authentication cookie valid.</span></span> <span data-ttu-id="418be-221">在这种情况下，将注销用户和用户名称将不再匹配的连接令牌中的用户名称。</span><span class="sxs-lookup"><span data-stu-id="418be-221">In that case, the user will be logged out and the user name will no longer match the user name in the connection token.</span></span> <span data-ttu-id="418be-222">可以通过添加一些定期请求的资源，在 web 服务器以保持身份验证 cookie 有效的脚本来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="418be-222">You can fix this problem by adding some script that periodically requests a resource on the web server to keep the authentication cookie valid.</span></span> <span data-ttu-id="418be-223">下面的示例演示如何请求每隔 30 分钟的资源。</span><span class="sxs-lookup"><span data-stu-id="418be-223">The following example shows how to request a resource every 30 minutes.</span></span>

[!code-javascript[Main](introduction-to-security/samples/sample4.js)]

<a id="autogen"></a>

### <a name="automatically-generated-javascript-proxy-files"></a><span data-ttu-id="418be-224">自动生成的 JavaScript 代理文件</span><span class="sxs-lookup"><span data-stu-id="418be-224">Automatically generated JavaScript proxy files</span></span>

<span data-ttu-id="418be-225">如果您不想要在每个用户的 JavaScript 代理文件中包含所有集线器和方法，可以禁用自动生成相应的文件。</span><span class="sxs-lookup"><span data-stu-id="418be-225">If you do not want to include all of the hubs and methods in the JavaScript proxy file for each user, you can disable the automatic generation of the file.</span></span> <span data-ttu-id="418be-226">如果您具有多个中心和方法，但不是希望每个用户需要注意的所有方法，可以选择此选项。</span><span class="sxs-lookup"><span data-stu-id="418be-226">You might choose this option if you have multiple hubs and methods, but do not want every user to be aware of all of the methods.</span></span> <span data-ttu-id="418be-227">通过设置禁用自动生成**EnableJavaScriptProxies**到**false**。</span><span class="sxs-lookup"><span data-stu-id="418be-227">You disable automatic generation by setting **EnableJavaScriptProxies** to **false**.</span></span>

[!code-csharp[Main](introduction-to-security/samples/sample5.cs)]

<span data-ttu-id="418be-228">有关 JavaScript 代理文件的详细信息，请参阅[生成的代理和它为您完成](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy)。</span><span class="sxs-lookup"><span data-stu-id="418be-228">For more information about the JavaScript proxy files, see [The generated proxy and what it does for you](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).</span></span> <a id="exceptions"></a>

### <a name="exceptions"></a><span data-ttu-id="418be-229">Exceptions</span><span class="sxs-lookup"><span data-stu-id="418be-229">Exceptions</span></span>

<span data-ttu-id="418be-230">应避免将异常对象传递到客户端，因为对象可能会公开给客户端的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="418be-230">You should avoid passing exception objects to clients because the objects may expose sensitive information to the clients.</span></span> <span data-ttu-id="418be-231">相反，在显示的相关错误消息的客户端上调用方法。</span><span class="sxs-lookup"><span data-stu-id="418be-231">Instead, call a method on the client that displays the relevant error message.</span></span>

[!code-csharp[Main](introduction-to-security/samples/sample6.cs)]
