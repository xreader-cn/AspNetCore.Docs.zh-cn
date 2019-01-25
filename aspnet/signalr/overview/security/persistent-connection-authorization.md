---
uid: signalr/overview/security/persistent-connection-authorization
title: 身份验证和授权 SignalR 永久性连接 |Microsoft Docs
author: bradygaster
description: 本主题介绍如何强制执行的持续性连接上的授权。 有关将安全集成到 SignalR 应用程序的常规信息...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: e264677b-9c01-47ec-94f9-3cd8f08f94af
msc.legacyurl: /signalr/overview/security/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: 7dab28f4720b34082f71e487c64af88a8ba01e6c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835617"
---
<a name="authentication-and-authorization-for-signalr-persistent-connections"></a><span data-ttu-id="e4844-104">身份验证和授权 SignalR 永久性连接</span><span class="sxs-lookup"><span data-stu-id="e4844-104">Authentication and Authorization for SignalR Persistent Connections</span></span>
====================
<span data-ttu-id="e4844-105">通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="e4844-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="e4844-106">本主题介绍如何强制执行的持续性连接上的授权。</span><span class="sxs-lookup"><span data-stu-id="e4844-106">This topic describes how to enforce authorization on a persistent connection.</span></span> <span data-ttu-id="e4844-107">有关将安全集成到 SignalR 应用程序的常规信息，请参阅[安全性简介](introduction-to-security.md)。</span><span class="sxs-lookup"><span data-stu-id="e4844-107">For general information about integrating security into a SignalR application, see [Introduction to Security](introduction-to-security.md).</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="e4844-108">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="e4844-108">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="e4844-109">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="e4844-109">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="e4844-110">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="e4844-110">.NET 4.5</span></span>
> - <span data-ttu-id="e4844-111">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="e4844-111">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="e4844-112">本主题的早期版本</span><span class="sxs-lookup"><span data-stu-id="e4844-112">Previous versions of this topic</span></span>
>
> <span data-ttu-id="e4844-113">有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。</span><span class="sxs-lookup"><span data-stu-id="e4844-113">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="e4844-114">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="e4844-114">Questions and comments</span></span>
>
> <span data-ttu-id="e4844-115">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="e4844-115">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="e4844-116">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="e4844-116">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="enforce-authorization"></a><span data-ttu-id="e4844-117">强制执行授权</span><span class="sxs-lookup"><span data-stu-id="e4844-117">Enforce authorization</span></span>

<span data-ttu-id="e4844-118">若要使用时强制实施授权规则[PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx)必须重写`AuthorizeRequest`方法。</span><span class="sxs-lookup"><span data-stu-id="e4844-118">To enforce authorization rules when using a [PersistentConnection](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) you must override the `AuthorizeRequest` method.</span></span> <span data-ttu-id="e4844-119">不能使用`Authorize`与持久连接的属性。</span><span class="sxs-lookup"><span data-stu-id="e4844-119">You cannot use the `Authorize` attribute with persistent connections.</span></span> <span data-ttu-id="e4844-120">`AuthorizeRequest`之前验证用户有权执行请求的操作的每个请求的 SignalR Framework 调用方法。</span><span class="sxs-lookup"><span data-stu-id="e4844-120">The `AuthorizeRequest` method is called by the SignalR Framework before every request to verify that the user is authorized to perform the requested action.</span></span> <span data-ttu-id="e4844-121">`AuthorizeRequest`不会从客户端调用方法; 相反，验证用户身份通过应用程序的标准身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="e4844-121">The `AuthorizeRequest` method is not called from the client; instead, you authenticate the user through your application's standard authentication mechanism.</span></span>

<span data-ttu-id="e4844-122">下面的示例演示如何将请求限制为已经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="e4844-122">The example below shows how to limit requests to authenticated users.</span></span>

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

<span data-ttu-id="e4844-123">您可以添加任何自定义的授权逻辑中的 AuthorizeRequest 方法;例如，检查用户是否属于特定角色。</span><span class="sxs-lookup"><span data-stu-id="e4844-123">You can add any customized authorization logic in the AuthorizeRequest method; such as, checking whether a user belongs to a particular role.</span></span>
