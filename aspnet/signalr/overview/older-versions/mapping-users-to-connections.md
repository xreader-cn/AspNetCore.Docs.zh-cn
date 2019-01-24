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
<a name="mapping-signalr-users-to-connections-in-signalr-1x"></a>SignalR 用户映射到连接中 SignalR 1.x
====================
通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本主题演示如何保留用户以及它们的连接有关的信息。


## <a name="introduction"></a>介绍

连接到中心的每个客户端传递唯一连接 id。可以检索此值在`Context.ConnectionId`集线器上下文的属性。 如果你的应用程序需要将用户映射到的连接 id 并保存该映射，可以使用以下项之一：

- [内存中存储](#inmemory)，如字典
- [SignalR 为每个用户的的组](#groups)
- [永久的、 外部存储](#database)，例如数据库表或 Azure 表存储

每种实现是本主题中所示。 您使用`OnConnected`， `OnDisconnected`，并`OnReconnected`方法的`Hub`类，以跟踪用户连接状态。

你的应用程序的最佳方法取决于：

- 托管应用程序的 web 服务器的数量。
- 您是否需要获取当前连接的用户的列表。
- 您是否需要应用程序或服务器重新启动时持久保存组和用户信息。
- 调用外部服务器的延迟是否出现问题。

下表显示了哪种方法适用于这些注意事项。

|  | 多个服务器 | 获取当前连接的用户的列表 | 保存后重新启动信息 | 获得最佳性能 |
| --- | --- | --- | --- | --- |
| 内存中 |  | ![](mapping-users-to-connections/_static/image1.png) |  | ![](mapping-users-to-connections/_static/image2.png) |
| 单用户组 | ![](mapping-users-to-connections/_static/image3.png) |  |  | ![](mapping-users-to-connections/_static/image4.png) |
| 永久的外部 | ![](mapping-users-to-connections/_static/image5.png) | ![](mapping-users-to-connections/_static/image6.png) | ![](mapping-users-to-connections/_static/image7.png) |  |

<a id="inmemory"></a>

## <a name="in-memory-storage"></a>内存中存储

以下示例演示如何保留存储在内存中字典中的连接和用户信息。 Dictionary 使用`HashSet`存储的连接 id。在任何时候用户可能具有多个连接到 SignalR 应用程序。 例如，通过多个设备或多个浏览器选项卡连接的用户将具有多个连接 id。

如果在应用程序关闭，所有信息都将丢失，但它也将进行重新填充，因为用户重新建立连接。 如果您的环境包括多个 web 服务器，因为每个服务器必须连接的单独集合，则内存中存储无效。

第一个示例显示了类，用于管理用户连接到的映射。 Hashset 集合的键将为该用户的名称。

[!code-csharp[Main](mapping-users-to-connections/samples/sample1.cs)]

下一个示例演示如何使用与集线器的连接映射类。 类的实例存储在变量名`_connections`。

[!code-csharp[Main](mapping-users-to-connections/samples/sample2.cs)]

<a id="groups"></a>

## <a name="single-user-groups"></a>单用户组

可以为每个用户创建组，然后将消息发送到该组时您想要访问只允许该用户。 每个组的名称是用户的名称。 如果用户具有多个连接，则将每个连接 id 添加到用户的组。

您不应手动删除用户从组用户断开连接时。 SignalR 框架自动执行此操作。

下面的示例演示如何实现单用户组。

[!code-csharp[Main](mapping-users-to-connections/samples/sample3.cs)]

<a id="database"></a>

## <a name="permanent-external-storage"></a>永久的、 外部存储

本主题演示如何使用数据库或 Azure 表存储来存储连接信息。 当你有多个 web 服务器，因为每个 web 服务器可以与同一个数据存储库进行交互时，这种方法有效。 如果你的 web 服务器停止工作或应用程序重新启动，`OnDisconnected`不会调用方法。 因此，很可能你的数据存储库将有不再有效的连接 id 的记录。 若要清理这些孤立的记录，你可能想要使之无效的与你的应用程序相关的时间范围外部创建的任何连接。 在本部分中的示例包括用于跟踪，创建连接时的值，但不是显示如何清理旧的记录，因为你可能想要执行该操作作为后台进程。

### <a name="database"></a>数据库

以下示例演示如何保留在数据库中的连接和用户信息。 可以使用任何数据访问技术;但是，下面的示例演示如何定义使用实体框架模型。 这些实体模型对应于数据库表和字段。 您的数据结构可能千差万别，具体取决于你的应用程序的要求。

第一个示例演示如何定义可以具有多个连接实体相关联的用户实体。

[!code-csharp[Main](mapping-users-to-connections/samples/sample4.cs)]

然后，从中心，可以使用如下所示的代码跟踪每个连接的状态。

[!code-csharp[Main](mapping-users-to-connections/samples/sample5.cs)]

### <a name="azure-table-storage"></a>Azure 表存储

下面的 Azure 表存储示例是类似于数据库的示例。 它不包括所有需要开始使用 Azure 表存储服务的信息。 有关信息，请参阅[如何通过.NET 使用表存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/)。

下面的示例演示用于存储连接信息的表实体。 它按用户名称，对数据进行分区，并由连接 id 标识的每个实体，因此用户可以在任何时间有多个连接。

[!code-csharp[Main](mapping-users-to-connections/samples/sample6.cs)]

在中心，你可以跟踪每个用户的连接的状态。

[!code-csharp[Main](mapping-users-to-connections/samples/sample7.cs)]
