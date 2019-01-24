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
<a name="authentication-and-authorization-for-signalr-hubs"></a><span data-ttu-id="e332d-104">身份验证和 SignalR 集线器的授权</span><span class="sxs-lookup"><span data-stu-id="e332d-104">Authentication and Authorization for SignalR Hubs</span></span>
====================
<span data-ttu-id="e332d-105">通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="e332d-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="e332d-106">本主题介绍如何限制哪些用户或角色可以访问中心的方法。</span><span class="sxs-lookup"><span data-stu-id="e332d-106">This topic describes how to restrict which users or roles can access hub methods.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="e332d-107">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="e332d-107">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="e332d-108">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="e332d-108">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="e332d-109">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="e332d-109">.NET 4.5</span></span>
> - <span data-ttu-id="e332d-110">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="e332d-110">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="e332d-111">本主题的早期版本</span><span class="sxs-lookup"><span data-stu-id="e332d-111">Previous versions of this topic</span></span>
>
> <span data-ttu-id="e332d-112">有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。</span><span class="sxs-lookup"><span data-stu-id="e332d-112">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="e332d-113">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="e332d-113">Questions and comments</span></span>
>
> <span data-ttu-id="e332d-114">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="e332d-114">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="e332d-115">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="e332d-115">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="e332d-116">概述</span><span class="sxs-lookup"><span data-stu-id="e332d-116">Overview</span></span>

<span data-ttu-id="e332d-117">本主题包含以下各节：</span><span class="sxs-lookup"><span data-stu-id="e332d-117">This topic contains the following sections:</span></span>

- [<span data-ttu-id="e332d-118">授权属性</span><span class="sxs-lookup"><span data-stu-id="e332d-118">Authorize attribute</span></span>](#authorizeattribute)
- [<span data-ttu-id="e332d-119">要求所有中心都进行身份验证</span><span class="sxs-lookup"><span data-stu-id="e332d-119">Require authentication for all hubs</span></span>](#requireauth)
- [<span data-ttu-id="e332d-120">自定义的授权</span><span class="sxs-lookup"><span data-stu-id="e332d-120">Customized authorization</span></span>](#custom)
- [<span data-ttu-id="e332d-121">将身份验证信息传递到客户端</span><span class="sxs-lookup"><span data-stu-id="e332d-121">Pass authentication information to clients</span></span>](#passauth)
- [<span data-ttu-id="e332d-122">.NET 客户端的身份验证选项</span><span class="sxs-lookup"><span data-stu-id="e332d-122">Authentication options for .NET clients</span></span>](#authoptions)

    - [<span data-ttu-id="e332d-123">使用窗体身份验证 cookie</span><span class="sxs-lookup"><span data-stu-id="e332d-123">Cookie with Forms Authentication</span></span>](#cookie)
    - [<span data-ttu-id="e332d-124">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="e332d-124">Windows Authentication</span></span>](#windows)
    - [<span data-ttu-id="e332d-125">Connection 标头</span><span class="sxs-lookup"><span data-stu-id="e332d-125">Connection header</span></span>](#header)
    - [<span data-ttu-id="e332d-126">证书</span><span class="sxs-lookup"><span data-stu-id="e332d-126">Certificate</span></span>](#certificate)

<a id="authorizeattribute"></a>

## <a name="authorize-attribute"></a><span data-ttu-id="e332d-127">授权属性</span><span class="sxs-lookup"><span data-stu-id="e332d-127">Authorize attribute</span></span>

<span data-ttu-id="e332d-128">提供 SignalR [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx)属性来指定哪些用户或角色有权访问的中心或方法。</span><span class="sxs-lookup"><span data-stu-id="e332d-128">SignalR provides the [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) attribute to specify which users or roles have access to a hub or method.</span></span> <span data-ttu-id="e332d-129">此属性位于`Microsoft.AspNet.SignalR`命名空间。</span><span class="sxs-lookup"><span data-stu-id="e332d-129">This attribute is located in the `Microsoft.AspNet.SignalR` namespace.</span></span> <span data-ttu-id="e332d-130">您将应用`Authorize`属性为中心或中心中的特定方法。</span><span class="sxs-lookup"><span data-stu-id="e332d-130">You apply the `Authorize` attribute to either a hub or particular methods in a hub.</span></span> <span data-ttu-id="e332d-131">当应用`Authorize`到 hub 类，指定的授权要求的特性应用于所有集线器中的方法。</span><span class="sxs-lookup"><span data-stu-id="e332d-131">When you apply the `Authorize` attribute to a hub class, the specified authorization requirement is applied to all of the methods in the hub.</span></span> <span data-ttu-id="e332d-132">本主题提供了不同类型的可以应用的授权要求的示例。</span><span class="sxs-lookup"><span data-stu-id="e332d-132">This topic provides examples of the different types of authorization requirements that you can apply.</span></span> <span data-ttu-id="e332d-133">无需`Authorize`属性，则连接客户端可以访问任何公共方法的中心。</span><span class="sxs-lookup"><span data-stu-id="e332d-133">Without the `Authorize` attribute, a connected client can access any public method on the hub.</span></span>

<span data-ttu-id="e332d-134">如果已定义 web 应用程序中名为"Admin"的角色，则可以指定该角色中的唯一用户可以访问用下面的代码中心。</span><span class="sxs-lookup"><span data-stu-id="e332d-134">If you have defined a role named "Admin" in your web application, you could specify that only users in that role can access a hub with the following code.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample1.cs)]

<span data-ttu-id="e332d-135">或者，你可以指定一个中心包含可供所有用户的一种方法和仅供经过身份验证的用户，如下所示的第二个方法。</span><span class="sxs-lookup"><span data-stu-id="e332d-135">Or, you can specify that a hub contains one method that is available to all users, and a second method that is only available to authenticated users, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample2.cs)]

<span data-ttu-id="e332d-136">下面的示例解决不同的授权方案：</span><span class="sxs-lookup"><span data-stu-id="e332d-136">The following examples address different authorization scenarios:</span></span>

- <span data-ttu-id="e332d-137">`[Authorize]` – 仅经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="e332d-137">`[Authorize]` – only authenticated users</span></span>
- <span data-ttu-id="e332d-138">`[Authorize(Roles = "Admin,Manager")]` – 仅经过身份验证中的指定角色的用户</span><span class="sxs-lookup"><span data-stu-id="e332d-138">`[Authorize(Roles = "Admin,Manager")]` – only authenticated users in the specified roles</span></span>
- <span data-ttu-id="e332d-139">`[Authorize(Users = "user1,user2")]` – 仅经过身份验证具有指定的用户名的用户</span><span class="sxs-lookup"><span data-stu-id="e332d-139">`[Authorize(Users = "user1,user2")]` – only authenticated users with the specified user names</span></span>
- <span data-ttu-id="e332d-140">`[Authorize(RequireOutgoing=false)]` – 仅经过身份验证的用户可以调用中心，但从服务器返回到客户端的调用不局限于授权，例如，只有特定用户可以发送一条消息，但所有其他人可以接收消息时。</span><span class="sxs-lookup"><span data-stu-id="e332d-140">`[Authorize(RequireOutgoing=false)]` – only authenticated users can invoke the hub, but calls from the server back to clients are not limited by authorization, such as, when only certain users can send a message but all others can receive the message.</span></span> <span data-ttu-id="e332d-141">RequireOutgoing 属性只能应用到整个的中心，不在中心内的个人方法。</span><span class="sxs-lookup"><span data-stu-id="e332d-141">The RequireOutgoing property can only be applied to the entire hub, not on individuals methods within the hub.</span></span> <span data-ttu-id="e332d-142">如果 RequireOutgoing 未设置为 false，满足授权要求的用户将调用从服务器中。</span><span class="sxs-lookup"><span data-stu-id="e332d-142">When RequireOutgoing is not set to false, only users that meet the authorization requirement are called from the server.</span></span>

<a id="requireauth"></a>

## <a name="require-authentication-for-all-hubs"></a><span data-ttu-id="e332d-143">要求所有中心都进行身份验证</span><span class="sxs-lookup"><span data-stu-id="e332d-143">Require authentication for all hubs</span></span>

<span data-ttu-id="e332d-144">你可以要求身份验证的所有集线器和集线器方法在应用程序中通过调用[RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx)方法时在应用程序启动。</span><span class="sxs-lookup"><span data-stu-id="e332d-144">You can require authentication for all hubs and hub methods in your application by calling the [RequireAuthentication](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) method when the application starts.</span></span> <span data-ttu-id="e332d-145">当你具有多个中心并想要为所有这些强制实施的身份验证要求时，可能会使用此方法。</span><span class="sxs-lookup"><span data-stu-id="e332d-145">You might use this method when you have multiple hubs and want to enforce an authentication requirement for all of them.</span></span> <span data-ttu-id="e332d-146">使用此方法，不能指定角色、 用户或传出的授权的要求。</span><span class="sxs-lookup"><span data-stu-id="e332d-146">With this method, you cannot specify requirements for role, user, or outgoing authorization.</span></span> <span data-ttu-id="e332d-147">仅可以指定到集线器方法的访问权限仅限于经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="e332d-147">You can only specify that access to the hub methods is restricted to authenticated users.</span></span> <span data-ttu-id="e332d-148">但是，仍可应用到中心或方法，以指定其他要求的 Authorize 属性。</span><span class="sxs-lookup"><span data-stu-id="e332d-148">However, you can still apply the Authorize attribute to hubs or methods to specify additional requirements.</span></span> <span data-ttu-id="e332d-149">在属性中指定的任何要求添加到身份验证的基本要求。</span><span class="sxs-lookup"><span data-stu-id="e332d-149">Any requirement you specify in an attribute is added to the basic requirement of authentication.</span></span>

<span data-ttu-id="e332d-150">下面的示例显示了将所有集线器方法都限制给已经过身份验证的用户的启动文件。</span><span class="sxs-lookup"><span data-stu-id="e332d-150">The following example shows a Startup file which restricts all hub methods to authenticated users.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample3.cs)]

<span data-ttu-id="e332d-151">如果您调用`RequireAuthentication()`方法处理 SignalR 请求后，将引发 SignalR`InvalidOperationException`异常。</span><span class="sxs-lookup"><span data-stu-id="e332d-151">If you call the `RequireAuthentication()` method after a SignalR request has been processed, SignalR will throw a `InvalidOperationException` exception.</span></span> <span data-ttu-id="e332d-152">因为不能将模块添加到 HubPipeline 调用管道后，SignalR 会引发此异常。</span><span class="sxs-lookup"><span data-stu-id="e332d-152">SignalR throws this exception because you cannot add a module to the HubPipeline after the pipeline has been invoked.</span></span> <span data-ttu-id="e332d-153">上面的示例演示调用`RequireAuthentication`中的方法`Configuration`方法执行之前处理的第一个请求一次。</span><span class="sxs-lookup"><span data-stu-id="e332d-153">The previous example shows calling the `RequireAuthentication` method in the `Configuration` method which is executed one time prior to handling the first request.</span></span>

<a id="custom"></a>

## <a name="customized-authorization"></a><span data-ttu-id="e332d-154">自定义的授权</span><span class="sxs-lookup"><span data-stu-id="e332d-154">Customized authorization</span></span>

<span data-ttu-id="e332d-155">如果需要自定义如何确定授权，则可以创建派生的类`AuthorizeAttribute`并重写[UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx)方法。</span><span class="sxs-lookup"><span data-stu-id="e332d-155">If you need to customize how authorization is determined, you can create a class that derives from `AuthorizeAttribute` and override the [UserAuthorized](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) method.</span></span> <span data-ttu-id="e332d-156">对于每个请求，SignalR 调用此方法来确定是否授权用户来完成该请求。</span><span class="sxs-lookup"><span data-stu-id="e332d-156">For each request, SignalR invokes this method to determine whether the user is authorized to complete the request.</span></span> <span data-ttu-id="e332d-157">在重写方法中，为你的授权方案提供必要的逻辑。</span><span class="sxs-lookup"><span data-stu-id="e332d-157">In the overridden method, you provide the necessary logic for your authorization scenario.</span></span> <span data-ttu-id="e332d-158">下面的示例演示如何强制实施通过基于声明的标识的授权。</span><span class="sxs-lookup"><span data-stu-id="e332d-158">The following example shows how to enforce authorization through claims-based identity.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample4.cs)]

<a id="passauth"></a>

## <a name="pass-authentication-information-to-clients"></a><span data-ttu-id="e332d-159">将身份验证信息传递到客户端</span><span class="sxs-lookup"><span data-stu-id="e332d-159">Pass authentication information to clients</span></span>

<span data-ttu-id="e332d-160">您可能需要在客户端运行的代码中使用的身份验证信息。</span><span class="sxs-lookup"><span data-stu-id="e332d-160">You may need to use authentication information in the code that runs on the client.</span></span> <span data-ttu-id="e332d-161">在客户端上调用方法时传递所需的信息。</span><span class="sxs-lookup"><span data-stu-id="e332d-161">You pass the required information when calling the methods on the client.</span></span> <span data-ttu-id="e332d-162">例如，聊天应用程序方法可以作为参数传递发布一条消息的人员的用户名称，如下所示。</span><span class="sxs-lookup"><span data-stu-id="e332d-162">For example, a chat application method could pass as a parameter the user name of the person posting a message, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample5.cs)]

<span data-ttu-id="e332d-163">或者，可以创建表示身份验证信息并将该对象作为参数传递的对象，如下所示。</span><span class="sxs-lookup"><span data-stu-id="e332d-163">Or, you can create an object to represent the authentication information and pass that object as a parameter, as shown below.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample6.cs)]

<span data-ttu-id="e332d-164">因为恶意用户无法使用它来模拟该客户端的请求，应永远不会将一台客户端连接 id 传递到其他客户端。</span><span class="sxs-lookup"><span data-stu-id="e332d-164">You should never pass one client's connection id to other clients, as a malicious user could use it to mimic a request from that client.</span></span>

<a id="authoptions"></a>

## <a name="authentication-options-for-net-clients"></a><span data-ttu-id="e332d-165">.NET 客户端的身份验证选项</span><span class="sxs-lookup"><span data-stu-id="e332d-165">Authentication options for .NET clients</span></span>

<span data-ttu-id="e332d-166">当具有.NET 客户端，例如一个控制台应用，用于与集线器被限制为身份验证的用户交互时，您可以传递中 cookie、 connection 标头或证书的身份验证凭据。</span><span class="sxs-lookup"><span data-stu-id="e332d-166">When you have a .NET client, such as a console app, which interacts with a hub that is limited to authenticated users, you can pass the authentication credentials in a cookie, the connection header, or a certificate.</span></span> <span data-ttu-id="e332d-167">在本部分中的示例演示如何使用这些不同的方法对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="e332d-167">The examples in this section show how to use those different methods for authenticating a user.</span></span> <span data-ttu-id="e332d-168">它们不是完全正常运行的 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="e332d-168">They are not fully-functional SignalR apps.</span></span> <span data-ttu-id="e332d-169">有关使用 SignalR.NET 客户端的详细信息，请参阅[中心 API 指南-.NET 客户端](../guide-to-the-api/hubs-api-guide-net-client.md)。</span><span class="sxs-lookup"><span data-stu-id="e332d-169">For more information about .NET clients with SignalR, see [Hubs API Guide - .NET Client](../guide-to-the-api/hubs-api-guide-net-client.md).</span></span>

<a id="cookie"></a>

### <a name="cookie"></a><span data-ttu-id="e332d-170">Cookie</span><span class="sxs-lookup"><span data-stu-id="e332d-170">Cookie</span></span>

<span data-ttu-id="e332d-171">在.NET 客户端与使用 ASP.NET 窗体身份验证的中心进行交互，您将需要手动将身份验证 cookie 设置在连接上。</span><span class="sxs-lookup"><span data-stu-id="e332d-171">When your .NET client interacts with a hub that uses ASP.NET Forms Authentication, you will need to manually set the authentication cookie on the connection.</span></span> <span data-ttu-id="e332d-172">添加到 cookie`CookieContainer`上的属性[HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx)对象。</span><span class="sxs-lookup"><span data-stu-id="e332d-172">You add the cookie to the `CookieContainer` property on the [HubConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) object.</span></span> <span data-ttu-id="e332d-173">下面的示例演示一个控制台应用，在网页上检索身份验证 cookie 并将该 cookie 添加到连接。</span><span class="sxs-lookup"><span data-stu-id="e332d-173">The following example shows a console app that retrieves an authentication cookie from a web page and adds that cookie to the connection.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample7.cs)]

<span data-ttu-id="e332d-174">此控制台应用发布到的凭据<strong>www.contoso.com/RemoteLogin</strong>其可以引用包含下面的代码隐藏文件的空页。</span><span class="sxs-lookup"><span data-stu-id="e332d-174">The console app posts the credentials to <strong>www.contoso.com/RemoteLogin</strong> which could refer to an empty page that contains the following code-behind file.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample8.cs)]

<a id="windows"></a>

### <a name="windows-authentication"></a><span data-ttu-id="e332d-175">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="e332d-175">Windows authentication</span></span>

<span data-ttu-id="e332d-176">当使用 Windows 身份验证，您可以通过传递当前用户的凭据[在](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx)属性。</span><span class="sxs-lookup"><span data-stu-id="e332d-176">When using Windows authentication, you can pass the current user's credentials by using the [DefaultCredentials](https://msdn.microsoft.com/library/system.net.credentialcache.defaultcredentials.aspx) property.</span></span> <span data-ttu-id="e332d-177">在的值设置为连接的凭据。</span><span class="sxs-lookup"><span data-stu-id="e332d-177">You set the credentials for the connection to the value of the DefaultCredentials.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample9.cs?highlight=6)]

<a id="header"></a>

### <a name="connection-header"></a><span data-ttu-id="e332d-178">Connection 标头</span><span class="sxs-lookup"><span data-stu-id="e332d-178">Connection header</span></span>

<span data-ttu-id="e332d-179">如果你的应用程序不使用 cookie，则可以连接标头中传递用户信息。</span><span class="sxs-lookup"><span data-stu-id="e332d-179">If your application is not using cookies, you can pass user information in the connection header.</span></span> <span data-ttu-id="e332d-180">例如，你可以连接标头中传递一个令牌。</span><span class="sxs-lookup"><span data-stu-id="e332d-180">For example, you can pass a token in the connection header.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample10.cs?highlight=6)]

<span data-ttu-id="e332d-181">然后，在中心，您将验证用户的令牌。</span><span class="sxs-lookup"><span data-stu-id="e332d-181">Then, in the hub, you would verify the user's token.</span></span>

<a id="certificate"></a>

### <a name="certificate"></a><span data-ttu-id="e332d-182">证书</span><span class="sxs-lookup"><span data-stu-id="e332d-182">Certificate</span></span>

<span data-ttu-id="e332d-183">可以传递要验证的用户的客户端证书。</span><span class="sxs-lookup"><span data-stu-id="e332d-183">You can pass a client certificate to verify the user.</span></span> <span data-ttu-id="e332d-184">创建连接时添加的证书。</span><span class="sxs-lookup"><span data-stu-id="e332d-184">You add the certificate when creating the connection.</span></span> <span data-ttu-id="e332d-185">下面的示例演示如何将客户端证书添加到连接;它不显示完整的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="e332d-185">The following example shows only how to add a client certificate to the connection; it does not show the full console app.</span></span> <span data-ttu-id="e332d-186">它使用[X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx)类提供多种不同的方式来创建的证书。</span><span class="sxs-lookup"><span data-stu-id="e332d-186">It uses the [X509Certificate](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate.aspx) class which provides several different ways to create the certificate.</span></span>

[!code-csharp[Main](hub-authorization/samples/sample11.cs?highlight=6)]
