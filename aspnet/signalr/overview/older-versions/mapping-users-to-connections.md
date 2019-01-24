---
uid: signalr/overview/older-versions/mapping-users-to-connections
title: SignalR 用户映射到连接中 SignalR 1.x |Microsoft Docs
author: bradygaster
description: 本主题演示如何保留用户以及它们的连接有关的信息。
ms.author: bradyg
ms.date: 10/17/2013
ms.assetid: ebbc93a8-e6c4-4122-8e0d-3aa42293c747
msc.legacyurl: /signalr/overview/older-versions/mapping-users-to-connections
msc.type: authoredcontent
ms.openlocfilehash: 1e65a11e08a5b060cf8b096b5fe5b90eb8dc5b51
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836709"
---
<a name="mapping-signalr-users-to-connections-in-signalr-1x"></a><span data-ttu-id="c245d-103">SignalR 用户映射到连接中 SignalR 1.x</span><span class="sxs-lookup"><span data-stu-id="c245d-103">Mapping SignalR Users to Connections in SignalR 1.x</span></span>
====================
<span data-ttu-id="c245d-104">通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="c245d-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="c245d-105">本主题演示如何保留用户以及它们的连接有关的信息。</span><span class="sxs-lookup"><span data-stu-id="c245d-105">This topic shows how to retain information about users and their connections.</span></span>


## <a name="introduction"></a><span data-ttu-id="c245d-106">介绍</span><span class="sxs-lookup"><span data-stu-id="c245d-106">Introduction</span></span>

<span data-ttu-id="c245d-107">连接到中心的每个客户端传递唯一连接 id。可以检索此值在`Context.ConnectionId`集线器上下文的属性。</span><span class="sxs-lookup"><span data-stu-id="c245d-107">Each client connecting to a hub passes a unique connection id. You can retrieve this value in the `Context.ConnectionId` property of the hub context.</span></span> <span data-ttu-id="c245d-108">如果你的应用程序需要将用户映射到的连接 id 并保存该映射，可以使用以下项之一：</span><span class="sxs-lookup"><span data-stu-id="c245d-108">If your application needs to map a user to the connection id and persist that mapping, you can use one of the following:</span></span>

- <span data-ttu-id="c245d-109">[内存中存储](#inmemory)，如字典</span><span class="sxs-lookup"><span data-stu-id="c245d-109">[In-memory storage](#inmemory), such as a dictionary</span></span>
- [<span data-ttu-id="c245d-110">SignalR 为每个用户的的组</span><span class="sxs-lookup"><span data-stu-id="c245d-110">SignalR group for each user</span></span>](#groups)
- <span data-ttu-id="c245d-111">[永久的、 外部存储](#database)，例如数据库表或 Azure 表存储</span><span class="sxs-lookup"><span data-stu-id="c245d-111">[Permanent, external storage](#database), such as a database table or Azure table storage</span></span>

<span data-ttu-id="c245d-112">每种实现是本主题中所示。</span><span class="sxs-lookup"><span data-stu-id="c245d-112">Each of these implementations is shown in this topic.</span></span> <span data-ttu-id="c245d-113">您使用`OnConnected`， `OnDisconnected`，并`OnReconnected`方法的`Hub`类，以跟踪用户连接状态。</span><span class="sxs-lookup"><span data-stu-id="c245d-113">You use the `OnConnected`, `OnDisconnected`, and `OnReconnected` methods of the `Hub` class to track the user connection status.</span></span>

<span data-ttu-id="c245d-114">你的应用程序的最佳方法取决于：</span><span class="sxs-lookup"><span data-stu-id="c245d-114">The best approach for your application depends on:</span></span>

- <span data-ttu-id="c245d-115">托管应用程序的 web 服务器的数量。</span><span class="sxs-lookup"><span data-stu-id="c245d-115">The number of web servers hosting your application.</span></span>
- <span data-ttu-id="c245d-116">您是否需要获取当前连接的用户的列表。</span><span class="sxs-lookup"><span data-stu-id="c245d-116">Whether you need to get a list of the currently connected users.</span></span>
- <span data-ttu-id="c245d-117">您是否需要应用程序或服务器重新启动时持久保存组和用户信息。</span><span class="sxs-lookup"><span data-stu-id="c245d-117">Whether you need to persist group and user information when the application or server restarts.</span></span>
- <span data-ttu-id="c245d-118">调用外部服务器的延迟是否出现问题。</span><span class="sxs-lookup"><span data-stu-id="c245d-118">Whether the latency of calling an external server is an issue.</span></span>

<span data-ttu-id="c245d-119">下表显示了哪种方法适用于这些注意事项。</span><span class="sxs-lookup"><span data-stu-id="c245d-119">The following table shows which approach works for these considerations.</span></span>

|  | <span data-ttu-id="c245d-120">多个服务器</span><span class="sxs-lookup"><span data-stu-id="c245d-120">More than one server</span></span> | <span data-ttu-id="c245d-121">获取当前连接的用户的列表</span><span class="sxs-lookup"><span data-stu-id="c245d-121">Get list of currently connected users</span></span> | <span data-ttu-id="c245d-122">保存后重新启动信息</span><span class="sxs-lookup"><span data-stu-id="c245d-122">Persist information after restarts</span></span> | <span data-ttu-id="c245d-123">获得最佳性能</span><span class="sxs-lookup"><span data-stu-id="c245d-123">Optimal performance</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="c245d-124">内存中</span><span class="sxs-lookup"><span data-stu-id="c245d-124">In-memory</span></span> |  | ![](mapping-users-to-connections/_static/image1.png) |  | ![](mapping-users-to-connections/_static/image2.png) |
| <span data-ttu-id="c245d-125">单用户组</span><span class="sxs-lookup"><span data-stu-id="c245d-125">Single-user groups</span></span> | ![](mapping-users-to-connections/_static/image3.png) |  |  | ![](mapping-users-to-connections/_static/image4.png) |
| <span data-ttu-id="c245d-126">永久的外部</span><span class="sxs-lookup"><span data-stu-id="c245d-126">Permanent, external</span></span> | ![](mapping-users-to-connections/_static/image5.png) | ![](mapping-users-to-connections/_static/image6.png) | ![](mapping-users-to-connections/_static/image7.png) |  |

<a id="inmemory"></a>

## <a name="in-memory-storage"></a><span data-ttu-id="c245d-127">内存中存储</span><span class="sxs-lookup"><span data-stu-id="c245d-127">In-memory storage</span></span>

<span data-ttu-id="c245d-128">以下示例演示如何保留存储在内存中字典中的连接和用户信息。</span><span class="sxs-lookup"><span data-stu-id="c245d-128">The following examples show how to retain connection and user information in a dictionary that is stored in memory.</span></span> <span data-ttu-id="c245d-129">Dictionary 使用`HashSet`存储的连接 id。在任何时候用户可能具有多个连接到 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="c245d-129">The dictionary uses a `HashSet` to store the connection id. At any time a user could have more than one connection to the SignalR application.</span></span> <span data-ttu-id="c245d-130">例如，通过多个设备或多个浏览器选项卡连接的用户将具有多个连接 id。</span><span class="sxs-lookup"><span data-stu-id="c245d-130">For example, a user who is connected through multiple devices or more than one browser tab would have more than one connection id.</span></span>

<span data-ttu-id="c245d-131">如果在应用程序关闭，所有信息都将丢失，但它也将进行重新填充，因为用户重新建立连接。</span><span class="sxs-lookup"><span data-stu-id="c245d-131">If the application shuts down, all of the information is lost, but it will be re-populated as the users re-establish their connections.</span></span> <span data-ttu-id="c245d-132">如果您的环境包括多个 web 服务器，因为每个服务器必须连接的单独集合，则内存中存储无效。</span><span class="sxs-lookup"><span data-stu-id="c245d-132">In-memory storage does not work if your environment includes more than one web server because each server would have a separate collection of connections.</span></span>

<span data-ttu-id="c245d-133">第一个示例显示了类，用于管理用户连接到的映射。</span><span class="sxs-lookup"><span data-stu-id="c245d-133">The first example shows a class that manages the mapping of users to connections.</span></span> <span data-ttu-id="c245d-134">Hashset 集合的键将为该用户的名称。</span><span class="sxs-lookup"><span data-stu-id="c245d-134">The key for the HashSet will be the user's name.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

<span data-ttu-id="c245d-135">下一个示例演示如何使用与集线器的连接映射类。</span><span class="sxs-lookup"><span data-stu-id="c245d-135">The next example shows how to use the connection mapping class from a hub.</span></span> <span data-ttu-id="c245d-136">类的实例存储在变量名`_connections`。</span><span class="sxs-lookup"><span data-stu-id="c245d-136">The instance of the class is stored in a variable name `_connections`.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a><span data-ttu-id="c245d-137">单用户组</span><span class="sxs-lookup"><span data-stu-id="c245d-137">Single-user groups</span></span>

<span data-ttu-id="c245d-138">可以为每个用户创建组，然后将消息发送到该组时您想要访问只允许该用户。</span><span class="sxs-lookup"><span data-stu-id="c245d-138">You can create a group for each user, and then send a message to that group when you want to reach only that user.</span></span> <span data-ttu-id="c245d-139">每个组的名称是用户的名称。</span><span class="sxs-lookup"><span data-stu-id="c245d-139">The name of each group is the name of the user.</span></span> <span data-ttu-id="c245d-140">如果用户具有多个连接，则将每个连接 id 添加到用户的组。</span><span class="sxs-lookup"><span data-stu-id="c245d-140">If a user has more than one connection, each connection id is added to the user's group.</span></span>

<span data-ttu-id="c245d-141">您不应手动删除用户从组用户断开连接时。</span><span class="sxs-lookup"><span data-stu-id="c245d-141">You should not manually remove the user from the group when the user disconnects.</span></span> <span data-ttu-id="c245d-142">SignalR 框架自动执行此操作。</span><span class="sxs-lookup"><span data-stu-id="c245d-142">This action is automatically performed by the SignalR framework.</span></span>

<span data-ttu-id="c245d-143">下面的示例演示如何实现单用户组。</span><span class="sxs-lookup"><span data-stu-id="c245d-143">The following example shows how to implement single-user groups.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a><span data-ttu-id="c245d-144">永久的、 外部存储</span><span class="sxs-lookup"><span data-stu-id="c245d-144">Permanent, external storage</span></span>

<span data-ttu-id="c245d-145">本主题演示如何使用数据库或 Azure 表存储来存储连接信息。</span><span class="sxs-lookup"><span data-stu-id="c245d-145">This topic shows how to use either a database or Azure table storage for storing connection information.</span></span> <span data-ttu-id="c245d-146">当你有多个 web 服务器，因为每个 web 服务器可以与同一个数据存储库进行交互时，这种方法有效。</span><span class="sxs-lookup"><span data-stu-id="c245d-146">This approach works when you have multiple web servers because each web server can interact with the same data repository.</span></span> <span data-ttu-id="c245d-147">如果你的 web 服务器停止工作或应用程序重新启动，`OnDisconnected`不会调用方法。</span><span class="sxs-lookup"><span data-stu-id="c245d-147">If your web servers stop working or the application restarts, the `OnDisconnected` method is not called.</span></span> <span data-ttu-id="c245d-148">因此，很可能你的数据存储库将有不再有效的连接 id 的记录。</span><span class="sxs-lookup"><span data-stu-id="c245d-148">Therefore, it is possible that your data repository will have records for connection ids that are no longer valid.</span></span> <span data-ttu-id="c245d-149">若要清理这些孤立的记录，你可能想要使之无效的与你的应用程序相关的时间范围外部创建的任何连接。</span><span class="sxs-lookup"><span data-stu-id="c245d-149">To clean up these orphaned records, you may wish to invalidate any connection that was created outside of a timeframe that is relevant to your application.</span></span> <span data-ttu-id="c245d-150">在本部分中的示例包括用于跟踪，创建连接时的值，但不是显示如何清理旧的记录，因为你可能想要执行该操作作为后台进程。</span><span class="sxs-lookup"><span data-stu-id="c245d-150">The examples in this section include a value for tracking when the connection was created, but do not show how to clean up old records because you may want to do that as background process.</span></span>

### <a name="database"></a><span data-ttu-id="c245d-151">数据库</span><span class="sxs-lookup"><span data-stu-id="c245d-151">Database</span></span>

<span data-ttu-id="c245d-152">以下示例演示如何保留在数据库中的连接和用户信息。</span><span class="sxs-lookup"><span data-stu-id="c245d-152">The following examples show how to retain connection and user information in a database.</span></span> <span data-ttu-id="c245d-153">可以使用任何数据访问技术;但是，下面的示例演示如何定义使用实体框架模型。</span><span class="sxs-lookup"><span data-stu-id="c245d-153">You can use any data access technology; however, the example below shows how to define models using Entity Framework.</span></span> <span data-ttu-id="c245d-154">这些实体模型对应于数据库表和字段。</span><span class="sxs-lookup"><span data-stu-id="c245d-154">These entity models correspond to database tables and fields.</span></span> <span data-ttu-id="c245d-155">您的数据结构可能千差万别，具体取决于你的应用程序的要求。</span><span class="sxs-lookup"><span data-stu-id="c245d-155">Your data structure could vary considerably depending on the requirements of your application.</span></span>

<span data-ttu-id="c245d-156">第一个示例演示如何定义可以具有多个连接实体相关联的用户实体。</span><span class="sxs-lookup"><span data-stu-id="c245d-156">The first example shows how to define a user entity that can be associated with many connection entities.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

<span data-ttu-id="c245d-157">然后，从中心，可以使用如下所示的代码跟踪每个连接的状态。</span><span class="sxs-lookup"><span data-stu-id="c245d-157">Then, from the hub, you can track the state of each connection with the code shown below.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

### <a name="azure-table-storage"></a><span data-ttu-id="c245d-158">Azure 表存储</span><span class="sxs-lookup"><span data-stu-id="c245d-158">Azure table storage</span></span>

<span data-ttu-id="c245d-159">下面的 Azure 表存储示例是类似于数据库的示例。</span><span class="sxs-lookup"><span data-stu-id="c245d-159">The following Azure table storage example is similar to the database example.</span></span> <span data-ttu-id="c245d-160">它不包括所有需要开始使用 Azure 表存储服务的信息。</span><span class="sxs-lookup"><span data-stu-id="c245d-160">It does not include all of the information that you would need to get started with Azure Table Storage Service.</span></span> <span data-ttu-id="c245d-161">有关信息，请参阅[如何通过.NET 使用表存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/)。</span><span class="sxs-lookup"><span data-stu-id="c245d-161">For information, see [How to use Table storage from .NET](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/).</span></span>

<span data-ttu-id="c245d-162">下面的示例演示用于存储连接信息的表实体。</span><span class="sxs-lookup"><span data-stu-id="c245d-162">The following example shows a table entity for storing connection information.</span></span> <span data-ttu-id="c245d-163">它按用户名称，对数据进行分区，并由连接 id 标识的每个实体，因此用户可以在任何时间有多个连接。</span><span class="sxs-lookup"><span data-stu-id="c245d-163">It partitions the data by user name, and identifies each entity by the connection id, so a user can have multiple connections at any time.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

<span data-ttu-id="c245d-164">在中心，你可以跟踪每个用户的连接的状态。</span><span class="sxs-lookup"><span data-stu-id="c245d-164">In the hub, you track the status of each user's connection.</span></span>

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]
