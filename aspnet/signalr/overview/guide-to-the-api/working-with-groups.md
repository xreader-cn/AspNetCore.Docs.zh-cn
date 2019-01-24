---
uid: signalr/overview/guide-to-the-api/working-with-groups
title: 使用 SignalR 中的组 |Microsoft Docs
author: bradygaster
description: 本主题介绍如何持久保存与中心 API 的组成员身份信息。
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: cd378ecd-3e9e-4236-b902-65916d85a048
msc.legacyurl: /signalr/overview/guide-to-the-api/working-with-groups
msc.type: authoredcontent
ms.openlocfilehash: 384b7e5f07fa46ea3cc32e5c18c3c2327b7aedd3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836800"
---
<a name="working-with-groups-in-signalr"></a><span data-ttu-id="c41c9-103">使用 SignalR 中的组</span><span class="sxs-lookup"><span data-stu-id="c41c9-103">Working with Groups in SignalR</span></span>
====================
<span data-ttu-id="c41c9-104">通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="c41c9-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="c41c9-105">本主题介绍如何将用户添加到组并将保留组成员身份信息。</span><span class="sxs-lookup"><span data-stu-id="c41c9-105">This topic describes how to add users to groups and persist group membership information.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="c41c9-106">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="c41c9-106">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="c41c9-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="c41c9-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="c41c9-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="c41c9-108">.NET 4.5</span></span>
> - <span data-ttu-id="c41c9-109">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="c41c9-109">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="c41c9-110">本主题的早期版本</span><span class="sxs-lookup"><span data-stu-id="c41c9-110">Previous versions of this topic</span></span>
>
> <span data-ttu-id="c41c9-111">有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。</span><span class="sxs-lookup"><span data-stu-id="c41c9-111">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="c41c9-112">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="c41c9-112">Questions and comments</span></span>
>
> <span data-ttu-id="c41c9-113">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="c41c9-113">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="c41c9-114">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="c41c9-114">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="overview"></a><span data-ttu-id="c41c9-115">概述</span><span class="sxs-lookup"><span data-stu-id="c41c9-115">Overview</span></span>

<span data-ttu-id="c41c9-116">SignalR 中的组提供一种方法将消息广播到连接的客户端的指定子集。</span><span class="sxs-lookup"><span data-stu-id="c41c9-116">Groups in SignalR provide a method for broadcasting messages to specified subsets of connected clients.</span></span> <span data-ttu-id="c41c9-117">一个组可以具有任意数量的客户端，并在客户端可以是任意数量的组的成员。</span><span class="sxs-lookup"><span data-stu-id="c41c9-117">A group can have any number of clients, and a client can be a member of any number of groups.</span></span> <span data-ttu-id="c41c9-118">您无需显式创建组。</span><span class="sxs-lookup"><span data-stu-id="c41c9-118">You don't have to explicitly create groups.</span></span> <span data-ttu-id="c41c9-119">实际上，第一次 Groups.Add，对的调用中指定其名称自动创建一个组和从它的成员身份中删除最后一次连接时将其删除。</span><span class="sxs-lookup"><span data-stu-id="c41c9-119">In effect, a group is automatically created the first time you specify its name in a call to Groups.Add, and it is deleted when you remove the last connection from membership in it.</span></span> <span data-ttu-id="c41c9-120">有关使用组的简介，请参阅[如何管理组成员身份从 Hub 类](hubs-api-guide-server.md#groupsfromhub)中心 API-Server 指南中。</span><span class="sxs-lookup"><span data-stu-id="c41c9-120">For an introduction to using groups, see [How to manage group membership from the Hub class](hubs-api-guide-server.md#groupsfromhub) in the Hubs API - Server Guide.</span></span>

<span data-ttu-id="c41c9-121">没有 API 用于获取组成员身份列表或组的列表。</span><span class="sxs-lookup"><span data-stu-id="c41c9-121">There is no API for getting a group membership list or a list of groups.</span></span> <span data-ttu-id="c41c9-122">SignalR 将消息发送到客户端和基于发布/订阅模型的组和服务器不会维护的组或组成员身份列表。</span><span class="sxs-lookup"><span data-stu-id="c41c9-122">SignalR sends messages to clients and groups based on a pub/sub model, and the server does not maintain lists of groups or group memberships.</span></span> <span data-ttu-id="c41c9-123">这有助于最大程度提高可伸缩性，因为只要将节点添加到 web 场中，SignalR 维护任何状态已传播到新节点。</span><span class="sxs-lookup"><span data-stu-id="c41c9-123">This helps maximize scalability, because whenever you add a node to a web farm, any state that SignalR maintains has to be propagated to the new node.</span></span>

<span data-ttu-id="c41c9-124">将用户添加到组使用`Groups.Add`方法，用户会收到消息定向到该组的当前连接的持续时间内，但该组中的用户的成员身份不会持久保留超出当前连接。</span><span class="sxs-lookup"><span data-stu-id="c41c9-124">When you add a user to a group using the `Groups.Add` method, the user receives messages directed to that group for the duration of the current connection, but the user's membership in that group is not persisted beyond the current connection.</span></span> <span data-ttu-id="c41c9-125">如果你想要永久保留关于组和组成员身份的信息，必须将该数据存储在存储库，例如数据库或 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="c41c9-125">If you want to permanently retain information about groups and group membership, you must store that data in a repository such as a database or Azure table storage.</span></span> <span data-ttu-id="c41c9-126">然后，每次用户连接到您的应用程序，你从存储库中检索用户所属的组并手动将该用户添加到这些组。</span><span class="sxs-lookup"><span data-stu-id="c41c9-126">Then, each time a user connects to your application, you retrieve from the repository which groups the user belongs to, and manually add that user to those groups.</span></span>

<span data-ttu-id="c41c9-127">当重新连接暂时中断后，用户会自动重新加入的以前分配的组。</span><span class="sxs-lookup"><span data-stu-id="c41c9-127">When reconnecting after a temporary disruption, the user automatically re-joins the previously-assigned groups.</span></span> <span data-ttu-id="c41c9-128">自动重新加入组仅适用于重新连接后，不是在建立新连接时。</span><span class="sxs-lookup"><span data-stu-id="c41c9-128">Automatically rejoining a group only applies when reconnecting, not when establishing a new connection.</span></span> <span data-ttu-id="c41c9-129">从包含以前已分配组的列表的客户端传递的数字签名的令牌。</span><span class="sxs-lookup"><span data-stu-id="c41c9-129">A digitally-signed token is passed from the client that contains the list of previously-assigned groups.</span></span> <span data-ttu-id="c41c9-130">如果你想要验证用户是否属于请求的组，则可以覆盖默认行为。</span><span class="sxs-lookup"><span data-stu-id="c41c9-130">If you want to verify whether the user belongs to the requested groups, you can override the default behavior.</span></span>

<span data-ttu-id="c41c9-131">本主题包括以下部分：</span><span class="sxs-lookup"><span data-stu-id="c41c9-131">This topic includes the following sections:</span></span>

- [<span data-ttu-id="c41c9-132">添加和删除用户</span><span class="sxs-lookup"><span data-stu-id="c41c9-132">Adding and removing users</span></span>](#add)
- [<span data-ttu-id="c41c9-133">调用组的成员</span><span class="sxs-lookup"><span data-stu-id="c41c9-133">Calling members of a group</span></span>](#call)
- [<span data-ttu-id="c41c9-134">在数据库中存储组成员身份</span><span class="sxs-lookup"><span data-stu-id="c41c9-134">Storing group membership in a database</span></span>](#storedatabase)
- [<span data-ttu-id="c41c9-135">在 Azure 表存储中存储组成员身份</span><span class="sxs-lookup"><span data-stu-id="c41c9-135">Storing group membership in Azure table storage</span></span>](#storeazuretable)
- [<span data-ttu-id="c41c9-136">重新连接时验证组成员身份</span><span class="sxs-lookup"><span data-stu-id="c41c9-136">Verifying group membership when reconnecting</span></span>](#verify)

<a id="add"></a>

## <a name="adding-and-removing-users"></a><span data-ttu-id="c41c9-137">添加和删除用户</span><span class="sxs-lookup"><span data-stu-id="c41c9-137">Adding and removing users</span></span>

<span data-ttu-id="c41c9-138">若要添加或从组中删除用户，请调用[外](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx)或[删除](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx)方法，并传入用户的连接 id 和组的名称作为参数。</span><span class="sxs-lookup"><span data-stu-id="c41c9-138">To add or remove users from a group, you call the [Add](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) or [Remove](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) methods, and pass in the user's connection id and group's name as parameters.</span></span> <span data-ttu-id="c41c9-139">不需要手动用户从组中删除连接结束时。</span><span class="sxs-lookup"><span data-stu-id="c41c9-139">You do not need to manually remove a user from a group when the connection ends.</span></span>

<span data-ttu-id="c41c9-140">下面的示例演示`Groups.Add`和`Groups.Remove`集线器方法中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="c41c9-140">The following example shows the `Groups.Add` and `Groups.Remove` methods used in Hub methods.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample1.cs?highlight=5,10)]

<span data-ttu-id="c41c9-141">`Groups.Add`和`Groups.Remove`方法以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="c41c9-141">The `Groups.Add` and `Groups.Remove` methods execute asynchronously.</span></span>

<span data-ttu-id="c41c9-142">如果你想要向组添加客户端并立即将消息发送到客户端使用的组，您必须确保 Groups.Add 方法首先完成。</span><span class="sxs-lookup"><span data-stu-id="c41c9-142">If you want to add a client to a group and immediately send a message to the client by using the group, you have to make sure that the Groups.Add method finishes first.</span></span> <span data-ttu-id="c41c9-143">以下代码示例演示如何执行该操作。</span><span class="sxs-lookup"><span data-stu-id="c41c9-143">The following code examples show how to do that.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample2.cs?highlight=1,3)]

<span data-ttu-id="c41c9-144">一般情况下，不应包含`await`调用时`Groups.Remove`方法因为想要删除的连接 id 可能不再可用。</span><span class="sxs-lookup"><span data-stu-id="c41c9-144">In general, you should not include `await` when calling the `Groups.Remove` method because the connection id that you are trying to remove might no longer be available.</span></span> <span data-ttu-id="c41c9-145">在这种情况下，`TaskCanceledException`后在请求超时时引发。如果你的应用程序必须确保，用户具有已从组中删除一条消息发送到组之前，您可以添加`await`之前`Groups.Remove`，然后捕获和`TaskCanceledException`可能引发的异常。</span><span class="sxs-lookup"><span data-stu-id="c41c9-145">In that case, `TaskCanceledException` is thrown after the request times out. If your application must ensure that the user has been removed from the group before sending a message to the group, you can add `await` before `Groups.Remove`, and then catch the `TaskCanceledException` exception that might be thrown.</span></span>

<a id="call"></a>

## <a name="calling-members-of-a-group"></a><span data-ttu-id="c41c9-146">调用组的成员</span><span class="sxs-lookup"><span data-stu-id="c41c9-146">Calling members of a group</span></span>

<span data-ttu-id="c41c9-147">下面的示例中所示，您可以将消息发送到的所有组的成员或仅指定的组的成员。</span><span class="sxs-lookup"><span data-stu-id="c41c9-147">You can send messages to all of the members of a group or only specified members of the group, as shown in the following examples.</span></span>

- <span data-ttu-id="c41c9-148">**所有**连接中指定的组的客户端。</span><span class="sxs-lookup"><span data-stu-id="c41c9-148">**All** connected clients in a specified group.</span></span>

    [!code-css[Main](working-with-groups/samples/sample3.css)]
- <span data-ttu-id="c41c9-149">所有连接中指定的组的客户端**除指定客户端**标识由连接 id。</span><span class="sxs-lookup"><span data-stu-id="c41c9-149">All connected clients in a specified group **except the specified clients**, identified by connection ID.</span></span>

    [!code-csharp[Main](working-with-groups/samples/sample4.cs)]
- <span data-ttu-id="c41c9-150">所有连接中指定的组的客户端**除调用客户端**。</span><span class="sxs-lookup"><span data-stu-id="c41c9-150">All connected clients in a specified group **except the calling client**.</span></span>

    [!code-css[Main](working-with-groups/samples/sample5.css)]

<a id="storedatabase"></a>

## <a name="storing-group-membership-in-a-database"></a><span data-ttu-id="c41c9-151">在数据库中存储组成员身份</span><span class="sxs-lookup"><span data-stu-id="c41c9-151">Storing group membership in a database</span></span>

<span data-ttu-id="c41c9-152">以下示例演示如何保留在数据库中的组和用户信息。</span><span class="sxs-lookup"><span data-stu-id="c41c9-152">The following examples show how to retain group and user information in a database.</span></span> <span data-ttu-id="c41c9-153">可以使用任何数据访问技术;但是，下面的示例演示如何定义使用实体框架模型。</span><span class="sxs-lookup"><span data-stu-id="c41c9-153">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="c41c9-154">这些实体模型对应于数据库表和字段。</span><span class="sxs-lookup"><span data-stu-id="c41c9-154">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="c41c9-155">您的数据结构可能千差万别，具体取决于你的应用程序的要求。</span><span class="sxs-lookup"><span data-stu-id="c41c9-155">Your data structure could vary considerably depending on the requirements of your application.</span></span> <span data-ttu-id="c41c9-156">此示例包含一个名为类`ConversationRoom`这将是唯一的应用程序，使用户能够加入有关不同主题，例如体育赛事或园艺对话。</span><span class="sxs-lookup"><span data-stu-id="c41c9-156">This example includes a class named `ConversationRoom` which would be unique to an application that enables users to join conversations about different subjects, such as sports or gardening.</span></span> <span data-ttu-id="c41c9-157">此示例还包括用于连接的类。</span><span class="sxs-lookup"><span data-stu-id="c41c9-157">This example also includes a class for the connections.</span></span> <span data-ttu-id="c41c9-158">连接类不是绝对必要的跟踪组成员身份，但经常是跟踪用户的可靠解决方案的一部分。</span><span class="sxs-lookup"><span data-stu-id="c41c9-158">The connection class is not absolutely required for tracking group membership but is frequently part of robust solution to tracking users.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample6.cs)]

<span data-ttu-id="c41c9-159">然后，在中心，可以从数据库中检索的组和用户信息并手动将用户添加到相应的组。</span><span class="sxs-lookup"><span data-stu-id="c41c9-159">Then, in the hub, you can retrieve the group and user information from the database and manually add the user to the appropriate groups.</span></span> <span data-ttu-id="c41c9-160">该示例不包括用于跟踪用户连接的代码。</span><span class="sxs-lookup"><span data-stu-id="c41c9-160">The example does not include code for tracking the user connections.</span></span> <span data-ttu-id="c41c9-161">在此示例中，`await`关键字不应用之前`Groups.Add`因为到组的成员无法立即发送一条消息。</span><span class="sxs-lookup"><span data-stu-id="c41c9-161">In this example, the `await` keyword is not applied before `Groups.Add` because a message is not immediately sent to members of the group.</span></span> <span data-ttu-id="c41c9-162">如果你想要添加新成员后，立即将消息发送到组的所有成员，您希望应用`await`关键字，以确保异步操作已完成。</span><span class="sxs-lookup"><span data-stu-id="c41c9-162">If you want to send a message to all members of the group immediately after adding the new member, you would want to apply the `await` keyword to make sure the asynchronous operation has completed.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample7.cs)]

<a id="storeazuretable"></a>

## <a name="storing-group-membership-in-azure-table-storage"></a><span data-ttu-id="c41c9-163">在 Azure 表存储中存储组成员身份</span><span class="sxs-lookup"><span data-stu-id="c41c9-163">Storing group membership in Azure table storage</span></span>

<span data-ttu-id="c41c9-164">使用 Azure 表存储来存储组和用户信息是类似于使用数据库。</span><span class="sxs-lookup"><span data-stu-id="c41c9-164">Using Azure table storage to store group and user information is similar to using a database.</span></span> <span data-ttu-id="c41c9-165">下面的示例显示了存储的用户名和组名称的表实体。</span><span class="sxs-lookup"><span data-stu-id="c41c9-165">The following example shows a table entity that stores the user name and group name.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample8.cs)]

<span data-ttu-id="c41c9-166">在中心，用户连接时检索分配的组。</span><span class="sxs-lookup"><span data-stu-id="c41c9-166">In the hub, you retrieve the assigned groups when the user connects.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample9.cs)]

<a id="verify"></a>

## <a name="verifying-group-membership-when-reconnecting"></a><span data-ttu-id="c41c9-167">重新连接时验证组成员身份</span><span class="sxs-lookup"><span data-stu-id="c41c9-167">Verifying group membership when reconnecting</span></span>

<span data-ttu-id="c41c9-168">默认情况下，SignalR 会自动重新分配用户到相应的组的临时中断后，如删除并重新建立了连接超时之前连接重新连接时。当重新连接后，在令牌中传递用户的组信息和在服务器上验证该令牌。</span><span class="sxs-lookup"><span data-stu-id="c41c9-168">By default, SignalR automatically re-assigns a user to the appropriate groups when reconnecting from a temporary disruption, such as when a connection is dropped and re-established before the connection times out. The user's group information is passed in a token when reconnecting, and that token is verified on the server.</span></span> <span data-ttu-id="c41c9-169">有关重新加入到组的用户的验证过程的信息，请参阅[重新连接时重新加入组](../security/introduction-to-security.md#rejoingroup)。</span><span class="sxs-lookup"><span data-stu-id="c41c9-169">For information about the verification process for rejoining users to groups, see [Rejoining groups when reconnecting](../security/introduction-to-security.md#rejoingroup).</span></span>

<span data-ttu-id="c41c9-170">一般情况下，应使用自动重新加入组上的重新连接的默认行为。</span><span class="sxs-lookup"><span data-stu-id="c41c9-170">In general, you should use the default behavior of automatically rejoining groups on reconnect.</span></span> <span data-ttu-id="c41c9-171">不应将 SignalR 组用作一种安全机制用于限制对敏感数据的访问。</span><span class="sxs-lookup"><span data-stu-id="c41c9-171">SignalR groups are not intended as a security mechanism for restricting access to sensitive data.</span></span> <span data-ttu-id="c41c9-172">但是，如果你的应用程序必须仔细检查用户的组成员身份，重新连接时，可以重写默认行为。</span><span class="sxs-lookup"><span data-stu-id="c41c9-172">However, if your application must double-check a user's group membership when reconnecting, you can override the default behavior.</span></span> <span data-ttu-id="c41c9-173">更改默认行为可以添加一种负担到你的数据库因为为每个重新连接，而不是只是当用户连接时，必须检索用户的组成员身份。</span><span class="sxs-lookup"><span data-stu-id="c41c9-173">Changing the default behavior can add a burden to your database because a user's group membership must be retrieved for each reconnection rather than just when the user connects.</span></span>

<span data-ttu-id="c41c9-174">如果你必须在验证组成员身份重新连接，请创建返回的已分配组列表的新中心管道模块，如下所示。</span><span class="sxs-lookup"><span data-stu-id="c41c9-174">If you must verify group membership on reconnect, create a new hub pipeline module that returns a list of assigned groups, as shown below.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample10.cs)]

<span data-ttu-id="c41c9-175">然后，将该模块添加到集线器管道，为以下突出显示部分。</span><span class="sxs-lookup"><span data-stu-id="c41c9-175">Then, add that module to the hub pipeline, as highlighted below.</span></span>

[!code-csharp[Main](working-with-groups/samples/sample11.cs?highlight=4)]
