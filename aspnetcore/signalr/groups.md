---
title: SignalR 中管理用户和组
author: bradygaster
description: ASP.NET Core SignalR 用户和组管理的概述。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 180f8b4551eea39cc340bf1d250f4575cb5f71ed
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087436"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="c016a-103">SignalR 中管理用户和组</span><span class="sxs-lookup"><span data-stu-id="c016a-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="c016a-104">通过[brennan，第 Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="c016a-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="c016a-105">SignalR 允许消息发送到与特定用户关联的所有连接，以及要命名的连接组。</span><span class="sxs-lookup"><span data-stu-id="c016a-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="c016a-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c016a-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="c016a-107">SignalR 中的用户</span><span class="sxs-lookup"><span data-stu-id="c016a-107">Users in SignalR</span></span>

<span data-ttu-id="c016a-108">SignalR，可将消息发送到与特定用户关联的所有连接。</span><span class="sxs-lookup"><span data-stu-id="c016a-108">SignalR allows you to send messages to all connections associated with a specific user.</span></span> <span data-ttu-id="c016a-109">默认情况下，使用 SignalR`ClaimTypes.NameIdentifier`从`ClaimsPrincipal`与作为用户标识符连接相关联。</span><span class="sxs-lookup"><span data-stu-id="c016a-109">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="c016a-110">单个用户可以向 SignalR 应用程序的多个连接。</span><span class="sxs-lookup"><span data-stu-id="c016a-110">A single user can have multiple connections to a SignalR app.</span></span> <span data-ttu-id="c016a-111">例如，用户无法连接其桌面，以及他们的手机上。</span><span class="sxs-lookup"><span data-stu-id="c016a-111">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="c016a-112">每个设备都有单独的 SignalR 连接，但它们具有相同的用户所有关联。</span><span class="sxs-lookup"><span data-stu-id="c016a-112">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="c016a-113">如果向用户发送一条消息，所有与该用户关联的连接收到的消息。</span><span class="sxs-lookup"><span data-stu-id="c016a-113">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="c016a-114">可以通过访问连接的用户标识符`Context.UserIdentifier`中心中的属性。</span><span class="sxs-lookup"><span data-stu-id="c016a-114">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in your hub.</span></span>

<span data-ttu-id="c016a-115">向特定用户发送消息，通过将传递到的用户标识符`User`函数集线器方法中，在下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="c016a-115">Send a message to a specific user by passing the user identifier to the `User` function in your hub method as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="c016a-116">用户标识符是区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c016a-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="c016a-117">SignalR 中的组</span><span class="sxs-lookup"><span data-stu-id="c016a-117">Groups in SignalR</span></span>

<span data-ttu-id="c016a-118">组是与名称相关联的连接的集合。</span><span class="sxs-lookup"><span data-stu-id="c016a-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="c016a-119">可以将消息发送到组中的所有连接。</span><span class="sxs-lookup"><span data-stu-id="c016a-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="c016a-120">组是发送到的连接或多个连接，因为组管理的应用程序的建议的方法。</span><span class="sxs-lookup"><span data-stu-id="c016a-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="c016a-121">连接可以是多个组的成员。</span><span class="sxs-lookup"><span data-stu-id="c016a-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="c016a-122">这使得组理想之选类似的聊天应用程序，其中每个房间可以表示为一个组。</span><span class="sxs-lookup"><span data-stu-id="c016a-122">This makes groups ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="c016a-123">可以添加到连接或通过组中删除`AddToGroupAsync`和`RemoveFromGroupAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="c016a-123">Connections can be added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

<span data-ttu-id="c016a-124">当连接重新连接时，不会保留组成员身份。</span><span class="sxs-lookup"><span data-stu-id="c016a-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="c016a-125">需要重新建立时重新加入组的连接。</span><span class="sxs-lookup"><span data-stu-id="c016a-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="c016a-126">不能进行计数的一组成员，因为此信息不可用，如果应用程序扩展到多个服务器。</span><span class="sxs-lookup"><span data-stu-id="c016a-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="c016a-127">若要使用组的同时保护资源的访问权限，请使用[身份验证和授权](xref:signalr/authn-and-authz)ASP.NET Core 中的功能。</span><span class="sxs-lookup"><span data-stu-id="c016a-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="c016a-128">如果您仅将用户添加到组的凭据有效该组时，发送到该组的消息将仅转到经过授权的用户。</span><span class="sxs-lookup"><span data-stu-id="c016a-128">If you only add users to a group when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="c016a-129">但是，组不是一项安全功能。</span><span class="sxs-lookup"><span data-stu-id="c016a-129">However, groups are not a security feature.</span></span> <span data-ttu-id="c016a-130">身份验证声明具有组却没有，如到期和吊销的功能。</span><span class="sxs-lookup"><span data-stu-id="c016a-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="c016a-131">如果撤消用户的权限访问组，您必须手动检测，并从组中删除它们。</span><span class="sxs-lookup"><span data-stu-id="c016a-131">If a user's permission to access the group is revoked, you have to manually detect that and remove them from the group.</span></span>

> [!NOTE]
> <span data-ttu-id="c016a-132">组名称是区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c016a-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="c016a-133">相关资源</span><span class="sxs-lookup"><span data-stu-id="c016a-133">Related resources</span></span>

* [<span data-ttu-id="c016a-134">入门</span><span class="sxs-lookup"><span data-stu-id="c016a-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c016a-135">中心</span><span class="sxs-lookup"><span data-stu-id="c016a-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c016a-136">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="c016a-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
