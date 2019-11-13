---
title: 在 ASP.NET Core 中使用中心 SignalR
author: bradygaster
description: 了解如何使用 ASP.NET Core SignalR中的中心。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/hubs
ms.openlocfilehash: f95766cab84bddff2c7c62f30bce1e6d1e43deab
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963805"
---
# <a name="use-hubs-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="e14f4-103">在 SignalR 中使用中心 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e14f4-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="e14f4-104">作者： [Rachel Appel](https://twitter.com/rachelappel)和[古柯 Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="e14f4-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="e14f4-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e14f4-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-opno-locsignalr-hub"></a><span data-ttu-id="e14f4-106">什么是 SignalR 中心</span><span class="sxs-lookup"><span data-stu-id="e14f4-106">What is a SignalR hub</span></span>

<span data-ttu-id="e14f4-107">利用 SignalR 集线器 API，你可以从服务器对连接的客户端调用方法。</span><span class="sxs-lookup"><span data-stu-id="e14f4-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="e14f4-108">在服务器代码中，您将定义由客户端调用的方法。</span><span class="sxs-lookup"><span data-stu-id="e14f4-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="e14f4-109">在客户端代码中，您将定义从服务器调用的方法。</span><span class="sxs-lookup"><span data-stu-id="e14f4-109">In the client code, you define methods that are called from the server.</span></span> SignalR<span data-ttu-id="e14f4-110"> 负责使实时的客户端到服务器和服务器到客户端的通信成为可能。</span><span class="sxs-lookup"><span data-stu-id="e14f4-110"> takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-opno-locsignalr-hubs"></a><span data-ttu-id="e14f4-111">配置 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="e14f4-111">Configure SignalR hubs</span></span>

<span data-ttu-id="e14f4-112">SignalR 中间件需要一些服务，这些服务通过调用 `services.AddSignalR`进行配置。</span><span class="sxs-lookup"><span data-stu-id="e14f4-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e14f4-113">将 SignalR 功能添加到 ASP.NET Core 应用程序时，请通过在 `Startup.Configure` 方法的 `app.UseEndpoints` 回调中调用 `endpoint.MapHub`，设置 SignalR 路由。</span><span class="sxs-lookup"><span data-stu-id="e14f4-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="e14f4-114">将 SignalR 功能添加到 ASP.NET Core 应用程序时，请通过在 `Startup.Configure` 方法中调用 `app.UseSignalR` 来设置 SignalR 路由。</span><span class="sxs-lookup"><span data-stu-id="e14f4-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="e14f4-115">创建和使用集线器</span><span class="sxs-lookup"><span data-stu-id="e14f4-115">Create and use hubs</span></span>

<span data-ttu-id="e14f4-116">通过声明从 `Hub`继承的类创建一个中心，并向其添加公共方法。</span><span class="sxs-lookup"><span data-stu-id="e14f4-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="e14f4-117">客户端可以调用定义为 `public`的方法。</span><span class="sxs-lookup"><span data-stu-id="e14f4-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="e14f4-118">您可以指定返回类型和参数（包括复杂类型和数组），就像在任何C#方法中一样。</span><span class="sxs-lookup"><span data-stu-id="e14f4-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> SignalR<span data-ttu-id="e14f4-119"> 处理参数和返回值中的复杂对象和数组的序列化和反序列化。</span><span class="sxs-lookup"><span data-stu-id="e14f4-119"> handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="e14f4-120">中心是暂时性的：</span><span class="sxs-lookup"><span data-stu-id="e14f4-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="e14f4-121">不要将状态存储在 hub 类的属性中。</span><span class="sxs-lookup"><span data-stu-id="e14f4-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="e14f4-122">每个中心方法调用在新的中心实例上执行。</span><span class="sxs-lookup"><span data-stu-id="e14f4-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="e14f4-123">调用依赖于中心保持活动状态的异步方法时，请使用 `await`。</span><span class="sxs-lookup"><span data-stu-id="e14f4-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="e14f4-124">例如，如果在没有 `await` 的情况下调用，则方法（如 `Clients.All.SendAsync(...)`）可能会失败，并且在 `SendAsync` 完成之前中心方法完成。</span><span class="sxs-lookup"><span data-stu-id="e14f4-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="e14f4-125">上下文对象</span><span class="sxs-lookup"><span data-stu-id="e14f4-125">The Context object</span></span>

<span data-ttu-id="e14f4-126">`Hub` 类具有一个 `Context` 属性，其中包含有关连接的信息的以下属性：</span><span class="sxs-lookup"><span data-stu-id="e14f4-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="e14f4-127">Property</span><span class="sxs-lookup"><span data-stu-id="e14f4-127">Property</span></span> | <span data-ttu-id="e14f4-128">描述</span><span class="sxs-lookup"><span data-stu-id="e14f4-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="e14f4-129">获取由 SignalR分配的连接的唯一 ID。</span><span class="sxs-lookup"><span data-stu-id="e14f4-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="e14f4-130">每个连接都有一个连接 ID。</span><span class="sxs-lookup"><span data-stu-id="e14f4-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="e14f4-131">获取[用户标识符](xref:signalr/groups)。</span><span class="sxs-lookup"><span data-stu-id="e14f4-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="e14f4-132">默认情况下，SignalR 使用与连接关联的 `ClaimsPrincipal` 中的 `ClaimTypes.NameIdentifier` 作为用户标识符。</span><span class="sxs-lookup"><span data-stu-id="e14f4-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="e14f4-133">获取与当前用户关联的 `ClaimsPrincipal`。</span><span class="sxs-lookup"><span data-stu-id="e14f4-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="e14f4-134">获取可用于在此连接的范围内共享数据的键/值集合。</span><span class="sxs-lookup"><span data-stu-id="e14f4-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="e14f4-135">数据可以存储在此集合中，它将在不同的集线器方法调用中持久保存。</span><span class="sxs-lookup"><span data-stu-id="e14f4-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="e14f4-136">获取连接上的可用功能的集合。</span><span class="sxs-lookup"><span data-stu-id="e14f4-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="e14f4-137">目前，在大多数情况下不需要此集合，因此不会对其进行详细介绍。</span><span class="sxs-lookup"><span data-stu-id="e14f4-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="e14f4-138">获取在连接中止时通知的 `CancellationToken`。</span><span class="sxs-lookup"><span data-stu-id="e14f4-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="e14f4-139">`Hub.Context` 还包含以下方法：</span><span class="sxs-lookup"><span data-stu-id="e14f4-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="e14f4-140">方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-140">Method</span></span> | <span data-ttu-id="e14f4-141">描述</span><span class="sxs-lookup"><span data-stu-id="e14f4-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="e14f4-142">返回连接的 `HttpContext`，如果连接不与 HTTP 请求关联，则为 `null`。</span><span class="sxs-lookup"><span data-stu-id="e14f4-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="e14f4-143">对于 HTTP 连接，可以使用此方法来获取 HTTP 标头和查询字符串等信息。</span><span class="sxs-lookup"><span data-stu-id="e14f4-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="e14f4-144">中止连接。</span><span class="sxs-lookup"><span data-stu-id="e14f4-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="e14f4-145">Clients 对象</span><span class="sxs-lookup"><span data-stu-id="e14f4-145">The Clients object</span></span>

<span data-ttu-id="e14f4-146">`Hub` 类具有一个 `Clients` 属性，其中包含服务器和客户端之间的通信的以下属性：</span><span class="sxs-lookup"><span data-stu-id="e14f4-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="e14f4-147">Property</span><span class="sxs-lookup"><span data-stu-id="e14f4-147">Property</span></span> | <span data-ttu-id="e14f4-148">描述</span><span class="sxs-lookup"><span data-stu-id="e14f4-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="e14f4-149">在所有连接的客户端上调用方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="e14f4-150">在调用集线器方法的客户端上调用方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="e14f4-151">在所有连接的客户端上调用方法，但调用方法的客户端除外</span><span class="sxs-lookup"><span data-stu-id="e14f4-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="e14f4-152">`Hub.Clients` 还包含以下方法：</span><span class="sxs-lookup"><span data-stu-id="e14f4-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="e14f4-153">方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-153">Method</span></span> | <span data-ttu-id="e14f4-154">描述</span><span class="sxs-lookup"><span data-stu-id="e14f4-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="e14f4-155">在所有连接的客户端（指定的连接除外）上调用方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="e14f4-156">在特定连接的客户端上调用方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="e14f4-157">在特定连接的客户端上调用方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="e14f4-158">对指定组中的所有连接调用方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="e14f4-159">对指定组中的所有连接调用方法，指定的连接除外</span><span class="sxs-lookup"><span data-stu-id="e14f4-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="e14f4-160">在多组连接上调用方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="e14f4-161">对一组连接调用方法，而不包括调用该集线器方法的客户端</span><span class="sxs-lookup"><span data-stu-id="e14f4-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="e14f4-162">对与特定用户关联的所有连接调用方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="e14f4-163">对与指定用户相关联的所有连接调用方法</span><span class="sxs-lookup"><span data-stu-id="e14f4-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="e14f4-164">上表中的每个属性或方法返回一个具有 `SendAsync` 方法的对象。</span><span class="sxs-lookup"><span data-stu-id="e14f4-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="e14f4-165">利用 `SendAsync` 方法，你可以提供要调用的客户端方法的名称和参数。</span><span class="sxs-lookup"><span data-stu-id="e14f4-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="e14f4-166">向客户端发送消息</span><span class="sxs-lookup"><span data-stu-id="e14f4-166">Send messages to clients</span></span>

<span data-ttu-id="e14f4-167">若要调用特定客户端，请使用 `Clients` 对象的属性。</span><span class="sxs-lookup"><span data-stu-id="e14f4-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="e14f4-168">在下面的示例中，有三种集线器方法：</span><span class="sxs-lookup"><span data-stu-id="e14f4-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="e14f4-169">`SendMessage` 使用 `Clients.All`将消息发送到所有连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="e14f4-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="e14f4-170">`SendMessageToCaller` 使用 `Clients.Caller`将消息发送回调用方。</span><span class="sxs-lookup"><span data-stu-id="e14f4-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="e14f4-171">`SendMessageToGroups` 向 `SignalR Users` 组中的所有客户端发送一条消息。</span><span class="sxs-lookup"><span data-stu-id="e14f4-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="e14f4-172">强类型中心</span><span class="sxs-lookup"><span data-stu-id="e14f4-172">Strongly typed hubs</span></span>

<span data-ttu-id="e14f4-173">使用 `SendAsync` 的缺点是它依赖于幻字符串来指定要调用的客户端方法。</span><span class="sxs-lookup"><span data-stu-id="e14f4-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="e14f4-174">如果客户端中的方法名称拼写错误或缺失，则这会使代码对运行时错误开放。</span><span class="sxs-lookup"><span data-stu-id="e14f4-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="e14f4-175">使用 `SendAsync` 的一种替代方法是使用 <xref:Microsoft.AspNetCore.SignalR.Hub%601>强键入 `Hub`。</span><span class="sxs-lookup"><span data-stu-id="e14f4-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="e14f4-176">在下面的示例中，已将 `ChatHub` 客户端方法提取到名为 `IChatClient`的接口。</span><span class="sxs-lookup"><span data-stu-id="e14f4-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="e14f4-177">此接口可用于重构前面的 `ChatHub` 示例。</span><span class="sxs-lookup"><span data-stu-id="e14f4-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="e14f4-178">使用 `Hub<IChatClient>` 启用对客户端方法的编译时检查。</span><span class="sxs-lookup"><span data-stu-id="e14f4-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="e14f4-179">这可以防止由于使用神奇字符串而导致的问题，因为 `Hub<T>` 只能提供对在接口中定义的方法的访问。</span><span class="sxs-lookup"><span data-stu-id="e14f4-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="e14f4-180">使用强类型 `Hub<T>` 禁用使用 `SendAsync`的能力。</span><span class="sxs-lookup"><span data-stu-id="e14f4-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="e14f4-181">接口上定义的任何方法仍可以定义为异步方法。</span><span class="sxs-lookup"><span data-stu-id="e14f4-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="e14f4-182">事实上，其中每个方法都应该返回 `Task`。</span><span class="sxs-lookup"><span data-stu-id="e14f4-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="e14f4-183">由于它是一个接口，因此请勿使用 `async` 关键字。</span><span class="sxs-lookup"><span data-stu-id="e14f4-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="e14f4-184">例如:</span><span class="sxs-lookup"><span data-stu-id="e14f4-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="e14f4-185">不会从方法名称中去除 `Async` 后缀。</span><span class="sxs-lookup"><span data-stu-id="e14f4-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="e14f4-186">除非使用 `.on('MyMethodAsync')`定义了客户端方法，否则不应使用 `MyMethodAsync` 作为名称。</span><span class="sxs-lookup"><span data-stu-id="e14f4-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="e14f4-187">更改集线器方法的名称</span><span class="sxs-lookup"><span data-stu-id="e14f4-187">Change the name of a hub method</span></span>

<span data-ttu-id="e14f4-188">默认情况下，服务器集线器方法名称是 .NET 方法的名称。</span><span class="sxs-lookup"><span data-stu-id="e14f4-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="e14f4-189">但是，可以使用[HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute)属性来更改此默认设置，并手动指定方法的名称。</span><span class="sxs-lookup"><span data-stu-id="e14f4-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="e14f4-190">调用方法时，客户端应使用此名称，而不是 .NET 方法名称。</span><span class="sxs-lookup"><span data-stu-id="e14f4-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="e14f4-191">处理连接事件</span><span class="sxs-lookup"><span data-stu-id="e14f4-191">Handle events for a connection</span></span>

<span data-ttu-id="e14f4-192">SignalR 中心 API 提供 `OnConnectedAsync` 和 `OnDisconnectedAsync` 虚拟方法来管理和跟踪连接。</span><span class="sxs-lookup"><span data-stu-id="e14f4-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="e14f4-193">重写 `OnConnectedAsync` 虚方法，以便在客户端连接到集线器时执行操作，如将其添加到组中。</span><span class="sxs-lookup"><span data-stu-id="e14f4-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="e14f4-194">重写 `OnDisconnectedAsync` 虚方法，以便在客户端断开连接时执行操作。</span><span class="sxs-lookup"><span data-stu-id="e14f4-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="e14f4-195">如果客户端故意断开连接（例如，通过调用 `connection.stop()`），`exception` 参数将 `null`。</span><span class="sxs-lookup"><span data-stu-id="e14f4-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="e14f4-196">但是，如果客户端由于错误（例如网络故障）而断开连接，则 `exception` 参数将包含描述失败的异常。</span><span class="sxs-lookup"><span data-stu-id="e14f4-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a><span data-ttu-id="e14f4-197">处理错误</span><span class="sxs-lookup"><span data-stu-id="e14f4-197">Handle errors</span></span>

<span data-ttu-id="e14f4-198">在中心方法中引发的异常将发送到调用方法的客户端。</span><span class="sxs-lookup"><span data-stu-id="e14f4-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="e14f4-199">在 JavaScript 客户端上，`invoke` 方法返回[Javascript 承诺](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)。</span><span class="sxs-lookup"><span data-stu-id="e14f4-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="e14f4-200">当客户端使用 `catch`收到包含附加到承诺的处理程序的错误时，将调用该处理程序并将其作为 JavaScript `Error` 对象进行传递。</span><span class="sxs-lookup"><span data-stu-id="e14f4-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="e14f4-201">如果中心引发异常，则不会关闭连接。</span><span class="sxs-lookup"><span data-stu-id="e14f4-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="e14f4-202">默认情况下，SignalR 会向客户端返回一般的错误消息。</span><span class="sxs-lookup"><span data-stu-id="e14f4-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="e14f4-203">例如:</span><span class="sxs-lookup"><span data-stu-id="e14f4-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="e14f4-204">意外的异常通常包含敏感信息，例如数据库连接失败时触发的异常中的数据库服务器的名称。</span><span class="sxs-lookup"><span data-stu-id="e14f4-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="e14f4-205">默认情况下，SignalR 不会公开这些详细的错误消息作为一种安全措施。</span><span class="sxs-lookup"><span data-stu-id="e14f4-205">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="e14f4-206">有关抑制异常详细信息的原因的详细信息，请参阅[安全注意事项一文](xref:signalr/security#exceptions)。</span><span class="sxs-lookup"><span data-stu-id="e14f4-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="e14f4-207">如果*要将*异常情况传播到客户端，则可以使用 `HubException` 类。</span><span class="sxs-lookup"><span data-stu-id="e14f4-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="e14f4-208">如果从中心方法引发 `HubException`，SignalR**会将**整个消息发送到客户端（未修改）。</span><span class="sxs-lookup"><span data-stu-id="e14f4-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR<span data-ttu-id="e14f4-209"> 仅将异常的 `Message` 属性发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="e14f4-209"> only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="e14f4-210">异常的堆栈跟踪和其他属性不适用于客户端。</span><span class="sxs-lookup"><span data-stu-id="e14f4-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="e14f4-211">相关资源</span><span class="sxs-lookup"><span data-stu-id="e14f4-211">Related resources</span></span>

* <span data-ttu-id="e14f4-212">[ASP.NET Core 的简介 SignalR](xref:signalr/introduction)</span><span class="sxs-lookup"><span data-stu-id="e14f4-212">[Intro to ASP.NET Core SignalR](xref:signalr/introduction)</span></span>
* [<span data-ttu-id="e14f4-213">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="e14f4-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="e14f4-214">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="e14f4-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
