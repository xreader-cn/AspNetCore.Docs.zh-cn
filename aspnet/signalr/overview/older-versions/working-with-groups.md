---
uid: signalr/overview/older-versions/working-with-groups
title: 使用 SignalR 中的组 1.x |Microsoft Docs
author: bradygaster
description: 本主题介绍如何持久保存与中心 API 的组成员身份信息。
ms.author: bradyg
ms.date: 10/21/2013
ms.assetid: 22929efd-68c9-4609-b76d-f8ba42fda01e
msc.legacyurl: /signalr/overview/older-versions/working-with-groups
msc.type: authoredcontent
ms.openlocfilehash: e8ef7b34af4341fb97f54e2aab1d8971cce46af3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836150"
---
<a name="working-with-groups-in-signalr-1x"></a>使用 SignalR 中的组 1.x
====================
通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本主题介绍如何将用户添加到组并将保留组成员身份信息。


## <a name="overview"></a>概述

SignalR 中的组提供一种方法将消息广播到连接的客户端的指定子集。 一个组可以具有任意数量的客户端，并在客户端可以是任意数量的组的成员。 您无需显式创建组。 实际上，第一次 Groups.Add，对的调用中指定其名称自动创建一个组和从它的成员身份中删除最后一次连接时将其删除。 有关使用组的简介，请参阅[如何管理组成员身份从 Hub 类](index.md)中心 API-Server 指南中。

没有 API 用于获取组成员身份列表或组的列表。 SignalR 将消息发送到客户端和基于发布/订阅模型的组和服务器不会维护的组或组成员身份列表。 这有助于最大程度提高可伸缩性，因为只要将节点添加到 web 场中，SignalR 维护任何状态已传播到新节点。

将用户添加到组使用`Groups.Add`方法，用户会收到消息定向到该组的当前连接的持续时间内，但该组中的用户的成员身份不会持久保留超出当前连接。 如果你想要永久保留关于组和组成员身份的信息，必须将该数据存储在存储库，例如数据库或 Azure 表存储。 然后，每次用户连接到您的应用程序，你从存储库中检索用户所属的组并手动将该用户添加到这些组。

当重新连接暂时中断后，用户会自动重新加入的以前分配的组。 自动重新加入组仅适用于重新连接后，不是在建立新连接时。 从包含以前已分配组的列表的客户端传递的数字签名的令牌。 如果你想要验证用户是否属于请求的组，则可以覆盖默认行为。

本主题包括以下部分：

- [添加和删除用户](#add)
- [调用组的成员](#call)
- [在数据库中存储组成员身份](#storedatabase)
- [在 Azure 表存储中存储组成员身份](#storeazuretable)
- [重新连接时验证组成员身份](#verify)

<a id="add"></a>

## <a name="adding-and-removing-users"></a>添加和删除用户

若要添加或从组中删除用户，请调用[外](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx)或[删除](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx)方法，并传入用户的连接 id 和组的名称作为参数。 不需要手动用户从组中删除连接结束时。

下面的示例演示`Groups.Add`和`Groups.Remove`集线器方法中使用的方法。

[!code-csharp[Main](working-with-groups/samples/sample1.cs?highlight=5,10)]

`Groups.Add`和`Groups.Remove`方法以异步方式执行。

如果你想要向组添加客户端并立即将消息发送到客户端使用的组，您必须确保 Groups.Add 方法首先完成。 下面的代码示例演示如何为此，请通过使用适用于.NET 4.5 中，并通过使用.NET 4 中的代码的代码。

#### <a name="asynchronous-net-45-example"></a>异步.NET 4.5 的示例

[!code-csharp[Main](working-with-groups/samples/sample2.cs?highlight=1,3)]

#### <a name="asynchronous-net-4-example"></a>异步.NET 4 示例

[!code-csharp[Main](working-with-groups/samples/sample3.cs?highlight=3-4)]

一般情况下，不应包含`await`调用时`Groups.Remove`方法因为想要删除的连接 id 可能不再可用。 在这种情况下，`TaskCanceledException`后在请求超时时引发。如果你的应用程序必须确保，用户具有已从组中删除一条消息发送到组之前，您可以添加`await`Groups.Remove，然后 catch 之前`TaskCanceledException`可能引发的异常。

<a id="call"></a>

## <a name="calling-members-of-a-group"></a>调用组的成员

下面的示例中所示，您可以将消息发送到的所有组的成员或仅指定的组的成员。

- **所有**连接中指定的组的客户端。 

    [!code-css[Main](working-with-groups/samples/sample4.css)]
- 所有连接中指定的组的客户端**除指定客户端**标识由连接 id。 

    [!code-csharp[Main](working-with-groups/samples/sample5.cs)]
- 所有连接中指定的组的客户端**除调用客户端**。 

    [!code-css[Main](working-with-groups/samples/sample6.css)]

<a id="storedatabase"></a>

## <a name="storing-group-membership-in-a-database"></a>在数据库中存储组成员身份

以下示例演示如何保留在数据库中的组和用户信息。 可以使用任何数据访问技术;但是，下面的示例演示如何定义使用实体框架模型。 这些实体模型对应于数据库表和字段。 您的数据结构可能千差万别，具体取决于你的应用程序的要求。 此示例包含一个名为类`ConversationRoom`这将是唯一的应用程序，使用户能够加入有关不同主题，例如体育赛事或园艺对话。 此示例还包括用于连接的类。 连接类不是绝对必要的跟踪组成员身份，但经常是跟踪用户的可靠解决方案的一部分。

[!code-csharp[Main](working-with-groups/samples/sample7.cs)]

然后，在中心，可以从数据库中检索的组和用户信息并手动将用户添加到相应的组。 该示例不包括用于跟踪用户连接的代码。 在此示例中，`await`关键字不应用之前`Groups.Add`因为到组的成员无法立即发送一条消息。 如果你想要添加新成员后，立即将消息发送到组的所有成员，您希望应用`await`关键字，以确保异步操作已完成。

[!code-csharp[Main](working-with-groups/samples/sample8.cs)]

<a id="storeazuretable"></a>

## <a name="storing-group-membership-in-azure-table-storage"></a>在 Azure 表存储中存储组成员身份

使用 Azure 表存储来存储组和用户信息是类似于使用数据库。 下面的示例显示了存储的用户名和组名称的表实体。

[!code-csharp[Main](working-with-groups/samples/sample9.cs)]

在中心，用户连接时检索分配的组。

[!code-csharp[Main](working-with-groups/samples/sample10.cs)]

<a id="verify"></a>

## <a name="verifying-group-membership-when-reconnecting"></a>重新连接时验证组成员身份

默认情况下，SignalR 会自动重新分配用户到相应的组的临时中断后，如删除并重新建立了连接超时之前连接重新连接时。当重新连接后，在令牌中传递用户的组信息和在服务器上验证该令牌。 有关重新加入到组的用户的验证过程的信息，请参阅[重新连接时重新加入组](index.md)。

一般情况下，应使用自动重新加入组上的重新连接的默认行为。 不应将 SignalR 组用作一种安全机制用于限制对敏感数据的访问。 但是，如果你的应用程序必须仔细检查用户的组成员身份，重新连接时，可以重写默认行为。 更改默认行为可以添加一种负担到你的数据库因为为每个重新连接，而不是只是当用户连接时，必须检索用户的组成员身份。

如果你必须在验证组成员身份重新连接，请创建返回的已分配组列表的新中心管道模块，如下所示。

[!code-csharp[Main](working-with-groups/samples/sample11.cs)]

然后，将该模块添加到集线器管道，为以下突出显示部分。

[!code-csharp[Main](working-with-groups/samples/sample12.cs?highlight=10)]
