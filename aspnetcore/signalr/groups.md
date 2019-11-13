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
ms.openlocfilehash: 59e90042ecbaf936602643bbdc3965e036426b26
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963812"
---
# <a name="manage-users-and-groups-in-opno-locsignalr"></a><span data-ttu-id="6461e-103">管理 SignalR 中的用户和组</span><span class="sxs-lookup"><span data-stu-id="6461e-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="6461e-104">作者： [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="6461e-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="6461e-105"> 允许将消息发送到与特定用户关联的所有连接以及命名连接组。</span><span class="sxs-lookup"><span data-stu-id="6461e-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="6461e-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6461e-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-opno-locsignalr"></a><span data-ttu-id="6461e-107">SignalR 中的用户</span><span class="sxs-lookup"><span data-stu-id="6461e-107">Users in SignalR</span></span>

SignalR<span data-ttu-id="6461e-108"> 允许向与特定用户关联的所有连接发送消息。</span><span class="sxs-lookup"><span data-stu-id="6461e-108"> allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="6461e-109">默认情况下，SignalR 使用与连接关联的 `ClaimsPrincipal` 中的 `ClaimTypes.NameIdentifier` 作为用户标识符。</span><span class="sxs-lookup"><span data-stu-id="6461e-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="6461e-110">单个用户可以有多个到 SignalR 应用的连接。</span><span class="sxs-lookup"><span data-stu-id="6461e-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="6461e-111">例如，用户可以连接到其桌面以及他们的手机上。</span><span class="sxs-lookup"><span data-stu-id="6461e-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="6461e-112">每个设备都有一个单独的 SignalR 连接，但它们都与同一用户关联。</span><span class="sxs-lookup"><span data-stu-id="6461e-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="6461e-113">如果向用户发送一条消息，则所有与该用户关联的连接都将收到该消息。</span><span class="sxs-lookup"><span data-stu-id="6461e-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="6461e-114">可以通过中心中的 `Context.UserIdentifier` 属性访问连接的用户标识符。</span><span class="sxs-lookup"><span data-stu-id="6461e-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="6461e-115">向特定用户发送一条消息，方法是将用户标识符传递到中心方法中的 `User` 函数，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="6461e-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="6461e-116">用户标识符区分大小写。</span><span class="sxs-lookup"><span data-stu-id="6461e-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-opno-locsignalr"></a><span data-ttu-id="6461e-117">SignalR 中的组</span><span class="sxs-lookup"><span data-stu-id="6461e-117">Groups in SignalR</span></span>

<span data-ttu-id="6461e-118">组是与某个名称关联的连接的集合。</span><span class="sxs-lookup"><span data-stu-id="6461e-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="6461e-119">可以将消息发送到组中的所有连接。</span><span class="sxs-lookup"><span data-stu-id="6461e-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="6461e-120">建议将组发送到一个或多个连接，因为这些组由应用程序管理。</span><span class="sxs-lookup"><span data-stu-id="6461e-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="6461e-121">连接可以是多个组的成员。</span><span class="sxs-lookup"><span data-stu-id="6461e-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="6461e-122">这使得组非常适合作为聊天应用程序，其中每个会议室都可以表示为一个组。</span><span class="sxs-lookup"><span data-stu-id="6461e-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="6461e-123">可以通过 `AddToGroupAsync` 和 `RemoveFromGroupAsync` 方法将连接添加到组或从组中删除连接。</span><span class="sxs-lookup"><span data-stu-id="6461e-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="6461e-124">连接重新连接时，不会保留组成员身份。</span><span class="sxs-lookup"><span data-stu-id="6461e-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="6461e-125">重新建立组后，连接需要重新加入组。</span><span class="sxs-lookup"><span data-stu-id="6461e-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="6461e-126">不能对组的成员进行计数，因为如果将应用程序扩展到多台服务器，此信息不可用。</span><span class="sxs-lookup"><span data-stu-id="6461e-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="6461e-127">若要在使用组时保护对资源的访问，请在 ASP.NET Core 中使用[身份验证和授权](xref:signalr/authn-and-authz)功能。</span><span class="sxs-lookup"><span data-stu-id="6461e-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="6461e-128">如果只在凭据对该组有效的情况下将用户添加到组中，则发送到该组的消息将只发送到已授权的用户。</span><span class="sxs-lookup"><span data-stu-id="6461e-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="6461e-129">但组不是一种安全功能。</span><span class="sxs-lookup"><span data-stu-id="6461e-129">However, groups are not a security feature.</span></span> <span data-ttu-id="6461e-130">身份验证声明具有组不具备的功能，如过期和吊销。</span><span class="sxs-lookup"><span data-stu-id="6461e-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="6461e-131">如果用户对组的访问权限被吊销，则必须手动检测并将其从组中删除。</span><span class="sxs-lookup"><span data-stu-id="6461e-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="6461e-132">组名区分大小写。</span><span class="sxs-lookup"><span data-stu-id="6461e-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="6461e-133">相关资源</span><span class="sxs-lookup"><span data-stu-id="6461e-133">Related resources</span></span>

* [<span data-ttu-id="6461e-134">入门</span><span class="sxs-lookup"><span data-stu-id="6461e-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="6461e-135">中心</span><span class="sxs-lookup"><span data-stu-id="6461e-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="6461e-136">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="6461e-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
