---
title: 管理 SignalR 中的用户和组
author: bradygaster
description: ASP.NET Core SignalR 用户和组管理的概述。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/groups
ms.openlocfilehash: 7e8c85dcbc46daa68988374f499f19a9d2cead47
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654246"
---
# <a name="manage-users-and-groups-in-opno-locsignalr"></a>管理 SignalR 中的用户和组

作者： [Brennan Conroy](https://github.com/BrennanConroy)

SignalR 允许将消息发送到与特定用户关联的所有连接以及命名连接组。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [（如何下载）](xref:index#how-to-download-a-sample)

## <a name="users-in-opno-locsignalr"></a>SignalR 中的用户

SignalR 允许向与特定用户关联的所有连接发送消息。 默认情况下，SignalR 使用与连接关联的 `ClaimsPrincipal` 中的 `ClaimTypes.NameIdentifier` 作为用户标识符。 单个用户可以有多个到 SignalR 应用的连接。 例如，用户可以连接到其桌面以及他们的手机上。 每个设备都有一个单独的 SignalR 连接，但它们都与同一用户关联。 如果向用户发送一条消息，则所有与该用户关联的连接都将收到该消息。 可以通过中心中的 `Context.UserIdentifier` 属性访问连接的用户标识符。

向特定用户发送一条消息，方法是将用户标识符传递到中心方法中的 `User` 函数，如以下示例中所示：

> [!NOTE]
> 用户标识符区分大小写。

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-opno-locsignalr"></a>SignalR 中的组

组是与某个名称关联的连接的集合。 可以将消息发送到组中的所有连接。 建议将组发送到一个或多个连接，因为这些组由应用程序管理。 连接可以是多个组的成员。 这使得组非常适合作为聊天应用程序，其中每个会议室都可以表示为一个组。 可以通过 `AddToGroupAsync` 和 `RemoveFromGroupAsync` 方法将连接添加到组或从组中删除连接。

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

连接重新连接时，不会保留组成员身份。 重新建立组后，连接需要重新加入组。 不能对组的成员进行计数，因为如果将应用程序扩展到多台服务器，此信息不可用。

若要在使用组时保护对资源的访问，请在 ASP.NET Core 中使用[身份验证和授权](xref:signalr/authn-and-authz)功能。 如果只在凭据对该组有效的情况下将用户添加到组中，则发送到该组的消息将只发送到已授权的用户。 但组不是一种安全功能。 身份验证声明具有组不具备的功能，如过期和吊销。 如果用户对组的访问权限被吊销，则必须手动检测并将其从组中删除。

> [!NOTE]
> 组名区分大小写。

## <a name="related-resources"></a>相关资源

* [入门](xref:tutorials/signalr)
* [中心](xref:signalr/hubs)
* [发布到 Azure](xref:signalr/publish-to-azure-web-app)
