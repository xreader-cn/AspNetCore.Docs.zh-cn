---
title: SignalR 中管理用户和组
author: bradygaster
description: ASP.NET Core SignalR 用户和组管理的概述。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 45f2bb44e03a586b7fc186525fdd3a2645c820d5
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667747"
---
# <a name="manage-users-and-groups-in-signalr"></a>SignalR 中管理用户和组

通过[brennan，第 Conroy](https://github.com/BrennanConroy)

SignalR 允许消息发送到与特定用户关联的所有连接，以及要命名的连接组。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [（如何下载）](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>SignalR 中的用户

SignalR，可将消息发送到与特定用户关联的所有连接。 默认情况下，使用 SignalR`ClaimTypes.NameIdentifier`从`ClaimsPrincipal`与作为用户标识符连接相关联。 单个用户可以向 SignalR 应用程序的多个连接。 例如，用户无法连接其桌面，以及他们的手机上。 每个设备都有单独的 SignalR 连接，但它们具有相同的用户所有关联。 如果向用户发送一条消息，所有与该用户关联的连接收到的消息。 可以通过访问连接的用户标识符`Context.UserIdentifier`中心中的属性。

向特定用户发送消息，通过将传递到的用户标识符`User`函数集线器方法中，在下面的示例所示：

> [!NOTE]
> 用户标识符是区分大小写。

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>SignalR 中的组

组是与名称相关联的连接的集合。 可以将消息发送到组中的所有连接。 组是发送到的连接或多个连接，因为组管理的应用程序的建议的方法。 连接可以是多个组的成员。 这使得组理想之选类似的聊天应用程序，其中每个房间可以表示为一个组。 可以添加到连接或通过组中删除`AddToGroupAsync`和`RemoveFromGroupAsync`方法。

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

当连接重新连接时，不会保留组成员身份。 需要重新建立时重新加入组的连接。 不能进行计数的一组成员，因为此信息不可用，如果应用程序扩展到多个服务器。

若要使用组的同时保护资源的访问权限，请使用[身份验证和授权](xref:signalr/authn-and-authz)ASP.NET Core 中的功能。 如果您仅将用户添加到组的凭据有效该组时，发送到该组的消息将仅转到经过授权的用户。 但是，组不是一项安全功能。 身份验证声明具有组却没有，如到期和吊销的功能。 如果撤消用户的权限访问组，您必须手动检测，并从组中删除它们。

> [!NOTE]
> 组名称是区分大小写。

## <a name="related-resources"></a>相关资源

* [入门](xref:tutorials/signalr)
* [中心](xref:signalr/hubs)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
