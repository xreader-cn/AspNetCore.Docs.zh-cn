---
uid: signalr/overview/guide-to-the-api/mapping-users-to-connections
title: SignalR 用户映射到连接 |Microsoft Docs
author: bradygaster
description: 本主题演示如何保留用户以及它们的连接有关的信息。 Patrick Fletcher 帮助编写此主题。 本主题中使用的软件版本...
ms.author: bradyg
ms.date: 12/30/2014
ms.assetid: f80c08b1-3f1f-432c-980c-c7b6edeb31b1
msc.legacyurl: /signalr/overview/guide-to-the-api/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: 786fc6bbc0b8d430770cf19d1647dbdba26347aa
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837190"
---
<a name="mapping-signalr-users-to-connections"></a><span data-ttu-id="690f9-105">SignalR 用户映射到连接</span><span class="sxs-lookup"><span data-stu-id="690f9-105">Mapping SignalR Users to Connections</span></span>
====================
<span data-ttu-id="690f9-106">通过[Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="690f9-106">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="690f9-107">本主题演示如何保留用户以及它们的连接有关的信息。</span><span class="sxs-lookup"><span data-stu-id="690f9-107">This topic shows how to retain information about users and their connections.</span></span>
>
> <span data-ttu-id="690f9-108">Patrick Fletcher 帮助编写此主题。</span><span class="sxs-lookup"><span data-stu-id="690f9-108">Patrick Fletcher helped write this topic.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="690f9-109">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="690f9-109">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="690f9-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="690f9-110">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="690f9-111">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="690f9-111">.NET 4.5</span></span>
> - <span data-ttu-id="690f9-112">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="690f9-112">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="690f9-113">本主题的早期版本</span><span class="sxs-lookup"><span data-stu-id="690f9-113">Previous versions of this topic</span></span>
>
> <span data-ttu-id="690f9-114">有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。</span><span class="sxs-lookup"><span data-stu-id="690f9-114">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="690f9-115">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="690f9-115">Questions and comments</span></span>
>
> <span data-ttu-id="690f9-116">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="690f9-116">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="690f9-117">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="690f9-117">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="introduction"></a><span data-ttu-id="690f9-118">介绍</span><span class="sxs-lookup"><span data-stu-id="690f9-118">Introduction</span></span>

<span data-ttu-id="690f9-119">连接到中心的每个客户端传递唯一连接 id。可以检索此值在`Context.ConnectionId`集线器上下文的属性。</span><span class="sxs-lookup"><span data-stu-id="690f9-119">Each client connecting to a hub passes a unique connection id. You can retrieve this value in the `Context.ConnectionId` property of the hub context.</span></span> <span data-ttu-id="690f9-120">如果你的应用程序需要将用户映射到的连接 id 并保存该映射，可以使用以下项之一：</span><span class="sxs-lookup"><span data-stu-id="690f9-120">If your application needs to map a user to the connection id and persist that mapping, you can use one of the following:</span></span>

- [<span data-ttu-id="690f9-121">用户 ID 提供者 (SignalR 2)</span><span class="sxs-lookup"><span data-stu-id="690f9-121">The User ID Provider (SignalR 2)</span></span>](#IUserIdProvider)
- <span data-ttu-id="690f9-122">[内存中存储](#inmemory)，如字典</span><span class="sxs-lookup"><span data-stu-id="690f9-122">[In-memory storage](#inmemory), such as a dictionary</span></span>
- [<span data-ttu-id="690f9-123">SignalR 为每个用户的的组</span><span class="sxs-lookup"><span data-stu-id="690f9-123">SignalR group for each user</span></span>](#groups)
- <span data-ttu-id="690f9-124">[永久的、 外部存储](#database)，例如数据库表或 Azure 表存储</span><span class="sxs-lookup"><span data-stu-id="690f9-124">[Permanent, external storage](#database), such as a database table or Azure table storage</span></span>

<span data-ttu-id="690f9-125">每种实现是本主题中所示。</span><span class="sxs-lookup"><span data-stu-id="690f9-125">Each of these implementations is shown in this topic.</span></span> <span data-ttu-id="690f9-126">您使用`OnConnected`， `OnDisconnected`，并`OnReconnected`方法的`Hub`类，以跟踪用户连接状态。</span><span class="sxs-lookup"><span data-stu-id="690f9-126">You use the `OnConnected`, `OnDisconnected`, and `OnReconnected` methods of the `Hub` class to track the user connection status.</span></span>

<span data-ttu-id="690f9-127">你的应用程序的最佳方法取决于：</span><span class="sxs-lookup"><span data-stu-id="690f9-127">The best approach for your application depends on:</span></span>

- <span data-ttu-id="690f9-128">托管应用程序的 web 服务器的数量。</span><span class="sxs-lookup"><span data-stu-id="690f9-128">The number of web servers hosting your application.</span></span>
- <span data-ttu-id="690f9-129">您是否需要获取当前连接的用户的列表。</span><span class="sxs-lookup"><span data-stu-id="690f9-129">Whether you need to get a list of the currently connected users.</span></span>
- <span data-ttu-id="690f9-130">您是否需要应用程序或服务器重新启动时持久保存组和用户信息。</span><span class="sxs-lookup"><span data-stu-id="690f9-130">Whether you need to persist group and user information when the application or server restarts.</span></span>
- <span data-ttu-id="690f9-131">调用外部服务器的延迟是否出现问题。</span><span class="sxs-lookup"><span data-stu-id="690f9-131">Whether the latency of calling an external server is an issue.</span></span>

<span data-ttu-id="690f9-132">下表显示了哪种方法适用于这些注意事项。</span><span class="sxs-lookup"><span data-stu-id="690f9-132">The following table shows which approach works for these considerations.</span></span>

|  | <span data-ttu-id="690f9-133">多个服务器</span><span class="sxs-lookup"><span data-stu-id="690f9-133">More than one server</span></span> | <span data-ttu-id="690f9-134">获取当前连接的用户的列表</span><span class="sxs-lookup"><span data-stu-id="690f9-134">Get list of currently connected users</span></span> | <span data-ttu-id="690f9-135">保存后重新启动信息</span><span class="sxs-lookup"><span data-stu-id="690f9-135">Persist information after restarts</span></span> | <span data-ttu-id="690f9-136">获得最佳性能</span><span class="sxs-lookup"><span data-stu-id="690f9-136">Optimal performance</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="690f9-137">用户标识提供程序</span><span class="sxs-lookup"><span data-stu-id="690f9-137">UserID Provider</span></span> | ![](mapping-users-to-connections/_static/image1.png) |  |  | ![](mapping-users-to-connections/_static/image2.png) |
| <span data-ttu-id="690f9-138">内存中</span><span class="sxs-lookup"><span data-stu-id="690f9-138">In-memory</span></span> |  | ![](mapping-users-to-connections/_static/image3.png) |  | ![](mapping-users-to-connections/_static/image4.png) |
| <span data-ttu-id="690f9-139">单用户组</span><span class="sxs-lookup"><span data-stu-id="690f9-139">Single-user groups</span></span> | ![](mapping-users-to-connections/_static/image5.png) |  |  | ![](mapping-users-to-connections/_static/image6.png) |
| <span data-ttu-id="690f9-140">永久的外部</span><span class="sxs-lookup"><span data-stu-id="690f9-140">Permanent, external</span></span> | ![](mapping-users-to-connections/_static/image7.png) | ![](mapping-users-to-connections/_static/image8.png) | ![](mapping-users-to-connections/_static/image9.png) |  |

<a id="IUserIdProvider"></a>

## <a name="iuserid-provider"></a><span data-ttu-id="690f9-141">IUserID 提供程序</span><span class="sxs-lookup"><span data-stu-id="690f9-141">IUserID provider</span></span>

<span data-ttu-id="690f9-142">此功能允许用户指定用户 Id 是基于通过新界面 IUserIdProvider IRequest。</span><span class="sxs-lookup"><span data-stu-id="690f9-142">This feature allows users to specify what the userId is based on an IRequest via a new interface IUserIdProvider.</span></span>

<span data-ttu-id="690f9-143">**IUserIdProvider**</span><span class="sxs-lookup"><span data-stu-id="690f9-143">**The IUserIdProvider**</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

<span data-ttu-id="690f9-144">默认情况下，将使用用户的实现`IPrincipal.Identity.Name`作为用户名。</span><span class="sxs-lookup"><span data-stu-id="690f9-144">By default, there will be an implementation that uses the user's `IPrincipal.Identity.Name` as the user name.</span></span> <span data-ttu-id="690f9-145">若要更改此设置，注册的实现`IUserIdProvider`与全局主机应用程序启动时：</span><span class="sxs-lookup"><span data-stu-id="690f9-145">To change this, register your implementation of `IUserIdProvider` with the global host when your application starts:</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<span data-ttu-id="690f9-146">从在中心内您将能够将消息发送到这些用户通过以下 API:</span><span class="sxs-lookup"><span data-stu-id="690f9-146">From within a hub, you'll be able to send messages to these users via the following API:</span></span>

<span data-ttu-id="690f9-147">**向特定用户发送一条消息**</span><span class="sxs-lookup"><span data-stu-id="690f9-147">**Sending a message to a specific user**</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs?highlight=5)]

<a id="inmemory"></a>

## <a name="in-memory-storage"></a><span data-ttu-id="690f9-148">内存中存储</span><span class="sxs-lookup"><span data-stu-id="690f9-148">In-memory storage</span></span>

<span data-ttu-id="690f9-149">以下示例演示如何保留存储在内存中字典中的连接和用户信息。</span><span class="sxs-lookup"><span data-stu-id="690f9-149">The following examples show how to retain connection and user information in a dictionary that is stored in memory.</span></span> <span data-ttu-id="690f9-150">Dictionary 使用`HashSet`存储的连接 id。在任何时候用户可能具有多个连接到 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="690f9-150">The dictionary uses a `HashSet` to store the connection id. At any time a user could have more than one connection to the SignalR application.</span></span> <span data-ttu-id="690f9-151">例如，通过多个设备或多个浏览器选项卡连接的用户将具有多个连接 id。</span><span class="sxs-lookup"><span data-stu-id="690f9-151">For example, a user who is connected through multiple devices or more than one browser tab would have more than one connection id.</span></span>

<span data-ttu-id="690f9-152">如果在应用程序关闭，所有信息都将丢失，但它也将进行重新填充，因为用户重新建立连接。</span><span class="sxs-lookup"><span data-stu-id="690f9-152">If the application shuts down, all of the information is lost, but it will be re-populated as the users re-establish their connections.</span></span> <span data-ttu-id="690f9-153">如果您的环境包括多个 web 服务器，因为每个服务器必须连接的单独集合，则内存中存储无效。</span><span class="sxs-lookup"><span data-stu-id="690f9-153">In-memory storage does not work if your environment includes more than one web server because each server would have a separate collection of connections.</span></span>

<span data-ttu-id="690f9-154">第一个示例显示了类，用于管理用户连接到的映射。</span><span class="sxs-lookup"><span data-stu-id="690f9-154">The first example shows a class that manages the mapping of users to connections.</span></span> <span data-ttu-id="690f9-155">Hashset 集合的键将为该用户的名称。</span><span class="sxs-lookup"><span data-stu-id="690f9-155">The key for the HashSet will be the user's name.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

<span data-ttu-id="690f9-156">下一个示例演示如何使用与集线器的连接映射类。</span><span class="sxs-lookup"><span data-stu-id="690f9-156">The next example shows how to use the connection mapping class from a hub.</span></span> <span data-ttu-id="690f9-157">类的实例存储在变量名`_connections`。</span><span class="sxs-lookup"><span data-stu-id="690f9-157">The instance of the class is stored in a variable name `_connections`.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a><span data-ttu-id="690f9-158">单用户组</span><span class="sxs-lookup"><span data-stu-id="690f9-158">Single-user groups</span></span>

<span data-ttu-id="690f9-159">可以为每个用户创建组，然后将消息发送到该组时您想要访问只允许该用户。</span><span class="sxs-lookup"><span data-stu-id="690f9-159">You can create a group for each user, and then send a message to that group when you want to reach only that user.</span></span> <span data-ttu-id="690f9-160">每个组的名称是用户的名称。</span><span class="sxs-lookup"><span data-stu-id="690f9-160">The name of each group is the name of the user.</span></span> <span data-ttu-id="690f9-161">如果用户具有多个连接，则将每个连接 id 添加到用户的组。</span><span class="sxs-lookup"><span data-stu-id="690f9-161">If a user has more than one connection, each connection id is added to the user's group.</span></span>

<span data-ttu-id="690f9-162">您不应手动删除用户从组用户断开连接时。</span><span class="sxs-lookup"><span data-stu-id="690f9-162">You should not manually remove the user from the group when the user disconnects.</span></span> <span data-ttu-id="690f9-163">SignalR 框架自动执行此操作。</span><span class="sxs-lookup"><span data-stu-id="690f9-163">This action is automatically performed by the SignalR framework.</span></span>

<span data-ttu-id="690f9-164">下面的示例演示如何实现单用户组。</span><span class="sxs-lookup"><span data-stu-id="690f9-164">The following example shows how to implement single-user groups.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a><span data-ttu-id="690f9-165">永久的、 外部存储</span><span class="sxs-lookup"><span data-stu-id="690f9-165">Permanent, external storage</span></span>

<span data-ttu-id="690f9-166">本主题演示如何使用数据库或 Azure 表存储来存储连接信息。</span><span class="sxs-lookup"><span data-stu-id="690f9-166">This topic shows how to use either a database or Azure table storage for storing connection information.</span></span> <span data-ttu-id="690f9-167">当你有多个 web 服务器，因为每个 web 服务器可以与同一个数据存储库进行交互时，这种方法有效。</span><span class="sxs-lookup"><span data-stu-id="690f9-167">This approach works when you have multiple web servers because each web server can interact with the same data repository.</span></span> <span data-ttu-id="690f9-168">如果你的 web 服务器停止工作或应用程序重新启动，`OnDisconnected`不会调用方法。</span><span class="sxs-lookup"><span data-stu-id="690f9-168">If your web servers stop working or the application restarts, the `OnDisconnected` method is not called.</span></span> <span data-ttu-id="690f9-169">因此，很可能你的数据存储库将有不再有效的连接 id 的记录。</span><span class="sxs-lookup"><span data-stu-id="690f9-169">Therefore, it is possible that your data repository will have records for connection ids that are no longer valid.</span></span> <span data-ttu-id="690f9-170">若要清理这些孤立的记录，你可能想要使之无效的与你的应用程序相关的时间范围外部创建的任何连接。</span><span class="sxs-lookup"><span data-stu-id="690f9-170">To clean up these orphaned records, you may wish to invalidate any connection that was created outside of a timeframe that is relevant to your application.</span></span> <span data-ttu-id="690f9-171">在本部分中的示例包括用于跟踪，创建连接时的值，但不是显示如何清理旧的记录，因为你可能想要执行该操作作为后台进程。</span><span class="sxs-lookup"><span data-stu-id="690f9-171">The examples in this section include a value for tracking when the connection was created, but do not show how to clean up old records because you may want to do that as background process.</span></span>

### <a name="database"></a><span data-ttu-id="690f9-172">数据库</span><span class="sxs-lookup"><span data-stu-id="690f9-172">Database</span></span>

<span data-ttu-id="690f9-173">以下示例演示如何保留在数据库中的连接和用户信息。</span><span class="sxs-lookup"><span data-stu-id="690f9-173">The following examples show how to retain connection and user information in a database.</span></span> <span data-ttu-id="690f9-174">可以使用任何数据访问技术;但是，下面的示例演示如何定义使用实体框架模型。</span><span class="sxs-lookup"><span data-stu-id="690f9-174">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="690f9-175">这些实体模型对应于数据库表和字段。</span><span class="sxs-lookup"><span data-stu-id="690f9-175">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="690f9-176">您的数据结构可能千差万别，具体取决于你的应用程序的要求。</span><span class="sxs-lookup"><span data-stu-id="690f9-176">Your data structure could vary considerably depending on the requirements of your application.</span></span>

<span data-ttu-id="690f9-177">第一个示例演示如何定义可以具有多个连接实体相关联的用户实体。</span><span class="sxs-lookup"><span data-stu-id="690f9-177">The first example shows how to define a user entity that can be associated with many connection entities.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]

<span data-ttu-id="690f9-178">然后，从中心，可以使用如下所示的代码跟踪每个连接的状态。</span><span class="sxs-lookup"><span data-stu-id="690f9-178">Then, from the hub, you can track the state of each connection with the code shown below.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample8.cs)]

<a id="azure"></a>
### <a name="azure-table-storage"></a><span data-ttu-id="690f9-179">Azure 表存储</span><span class="sxs-lookup"><span data-stu-id="690f9-179">Azure table storage</span></span>

<span data-ttu-id="690f9-180">下面的 Azure 表存储示例是类似于数据库的示例。</span><span class="sxs-lookup"><span data-stu-id="690f9-180">The following Azure table storage example is similar to the database example.</span></span> <span data-ttu-id="690f9-181">它不包括所有需要开始使用 Azure 表存储服务的信息。</span><span class="sxs-lookup"><span data-stu-id="690f9-181">It does not include all of the information that you would need to get started with Azure Table Storage Service.</span></span> <span data-ttu-id="690f9-182">有关信息，请参阅[如何通过.NET 使用表存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/)。</span><span class="sxs-lookup"><span data-stu-id="690f9-182">For information, see [How to use Table storage from .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span></span>

<span data-ttu-id="690f9-183">下面的示例演示用于存储连接信息的表实体。</span><span class="sxs-lookup"><span data-stu-id="690f9-183">The following example shows a table entity for storing connection information.</span></span> <span data-ttu-id="690f9-184">它按用户名称，对数据进行分区，并由连接 id 标识的每个实体，因此用户可以在任何时间有多个连接。</span><span class="sxs-lookup"><span data-stu-id="690f9-184">It partitions the data by user name, and identifies each entity by the connection id, so a user can have multiple connections at any time.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample9.cs)]

<span data-ttu-id="690f9-185">在中心，你可以跟踪每个用户的连接的状态。</span><span class="sxs-lookup"><span data-stu-id="690f9-185">In the hub, you track the status of each user's connection.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample10.cs)]
