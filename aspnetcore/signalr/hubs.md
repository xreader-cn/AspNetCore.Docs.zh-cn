---
title: 在 ASP.NET Core SignalR 使用中心
author: bradygaster
description: 了解如何在 ASP.NET Core SignalR 中使用中心。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: f037c1f6fd7ef773b8e7b2fd4fdf6e28222c441a
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327256"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a><span data-ttu-id="4d900-103">ASP.NET Core 使用 SignalR 中的中心</span><span class="sxs-lookup"><span data-stu-id="4d900-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="4d900-104">通过[Rachel Appel](https://twitter.com/rachelappel)和[Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="4d900-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="4d900-105">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4d900-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-signalr-hub"></a><span data-ttu-id="4d900-106">SignalR 中心是什么</span><span class="sxs-lookup"><span data-stu-id="4d900-106">What is a SignalR hub</span></span>

<span data-ttu-id="4d900-107">SignalR 中心 API，可从服务器在连接的客户端上调用方法。</span><span class="sxs-lookup"><span data-stu-id="4d900-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="4d900-108">在服务器代码中，定义客户端调用的方法。</span><span class="sxs-lookup"><span data-stu-id="4d900-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="4d900-109">在客户端代码中，定义从服务器调用的方法。</span><span class="sxs-lookup"><span data-stu-id="4d900-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="4d900-110">SignalR 负责在后台实现实时的客户端到服务器和服务器到客户端通信的所有内容。</span><span class="sxs-lookup"><span data-stu-id="4d900-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-signalr-hubs"></a><span data-ttu-id="4d900-111">配置 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="4d900-111">Configure SignalR hubs</span></span>

<span data-ttu-id="4d900-112">SignalR 中间件需要一些服务，这些服务通过调用`services.AddSignalR`来配置。</span><span class="sxs-lookup"><span data-stu-id="4d900-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

<span data-ttu-id="4d900-113">将 SignalR 功能添加到 ASP.NET Core 应用程序时，通过在`Startup.Configure`方法中调用`app.UseSignalR`来设置 SignalR 路由。</span><span class="sxs-lookup"><span data-stu-id="4d900-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

## <a name="create-and-use-hubs"></a><span data-ttu-id="4d900-114">创建和使用中心</span><span class="sxs-lookup"><span data-stu-id="4d900-114">Create and use hubs</span></span>

<span data-ttu-id="4d900-115">通过声明从`Hub`继承的类来创建中心，并向其添加公共方法。</span><span class="sxs-lookup"><span data-stu-id="4d900-115">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="4d900-116">客户端可以调用定义为`public`的方法。</span><span class="sxs-lookup"><span data-stu-id="4d900-116">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="4d900-117">您可以指定返回类型和参数，包括复杂类型和数组，就像在任何 C# 方法中。</span><span class="sxs-lookup"><span data-stu-id="4d900-117">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="4d900-118">SignalR 处理序列化和反序列化复杂对象和数组中参数和返回值。</span><span class="sxs-lookup"><span data-stu-id="4d900-118">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="4d900-119">中心是暂时的：</span><span class="sxs-lookup"><span data-stu-id="4d900-119">Hubs are transient:</span></span>
>
> * <span data-ttu-id="4d900-120">不要将状态存储在 hub 类的属性中。</span><span class="sxs-lookup"><span data-stu-id="4d900-120">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="4d900-121">每个 hub 方法调用都在新的 hub 实例上执行。</span><span class="sxs-lookup"><span data-stu-id="4d900-121">Every hub method call is executed on a new hub instance.</span></span>  
> * <span data-ttu-id="4d900-122">使用`await`调用取决于中心保持活动状态的异步方法时。</span><span class="sxs-lookup"><span data-stu-id="4d900-122">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="4d900-123">例如，如方法`Clients.All.SendAsync(...)`如果调用，但不可能会失败`await`和集线器方法完成之前`SendAsync`完成。</span><span class="sxs-lookup"><span data-stu-id="4d900-123">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="4d900-124">上下文对象</span><span class="sxs-lookup"><span data-stu-id="4d900-124">The Context object</span></span>

<span data-ttu-id="4d900-125">`Hub`类具有`Context`属性，其中包含以下属性与连接有关的信息：</span><span class="sxs-lookup"><span data-stu-id="4d900-125">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="4d900-126">属性</span><span class="sxs-lookup"><span data-stu-id="4d900-126">Property</span></span> | <span data-ttu-id="4d900-127">描述</span><span class="sxs-lookup"><span data-stu-id="4d900-127">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="4d900-128">获取用于此连接，由 SignalR 分配的唯一 ID。</span><span class="sxs-lookup"><span data-stu-id="4d900-128">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="4d900-129">没有为每个连接的一个连接 ID。</span><span class="sxs-lookup"><span data-stu-id="4d900-129">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="4d900-130">获取[用户标识符](xref:signalr/groups)。</span><span class="sxs-lookup"><span data-stu-id="4d900-130">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="4d900-131">默认情况下，使用 SignalR`ClaimTypes.NameIdentifier`从`ClaimsPrincipal`与作为用户标识符连接相关联。</span><span class="sxs-lookup"><span data-stu-id="4d900-131">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="4d900-132">获取`ClaimsPrincipal`与当前用户相关联。</span><span class="sxs-lookup"><span data-stu-id="4d900-132">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="4d900-133">获取可用于共享此连接的作用域内的数据的键/值集合。</span><span class="sxs-lookup"><span data-stu-id="4d900-133">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="4d900-134">数据可以存储在此集合中，它将连接在不同的集线器方法调用中保持原样。</span><span class="sxs-lookup"><span data-stu-id="4d900-134">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="4d900-135">获取在连接上的可用功能的集合。</span><span class="sxs-lookup"><span data-stu-id="4d900-135">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="4d900-136">现在，此集合不需要在大多数情况下，因此它不尚未记录在详细信息。</span><span class="sxs-lookup"><span data-stu-id="4d900-136">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="4d900-137">获取`CancellationToken`，时连接中止通知。</span><span class="sxs-lookup"><span data-stu-id="4d900-137">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="4d900-138">`Hub.Context` 此外包含以下方法：</span><span class="sxs-lookup"><span data-stu-id="4d900-138">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="4d900-139">方法</span><span class="sxs-lookup"><span data-stu-id="4d900-139">Method</span></span> | <span data-ttu-id="4d900-140">描述</span><span class="sxs-lookup"><span data-stu-id="4d900-140">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="4d900-141">返回`HttpContext`用于此连接，或`null`如果连接不是与 HTTP 请求相关联。</span><span class="sxs-lookup"><span data-stu-id="4d900-141">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="4d900-142">对于 HTTP 连接，可以使用此方法以获取 HTTP 标头和查询字符串等信息。</span><span class="sxs-lookup"><span data-stu-id="4d900-142">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="4d900-143">中止的连接。</span><span class="sxs-lookup"><span data-stu-id="4d900-143">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="4d900-144">客户端对象</span><span class="sxs-lookup"><span data-stu-id="4d900-144">The Clients object</span></span>

<span data-ttu-id="4d900-145">`Hub`类具有`Clients`属性，其中包含服务器和客户端之间通信的以下属性：</span><span class="sxs-lookup"><span data-stu-id="4d900-145">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="4d900-146">属性</span><span class="sxs-lookup"><span data-stu-id="4d900-146">Property</span></span> | <span data-ttu-id="4d900-147">描述</span><span class="sxs-lookup"><span data-stu-id="4d900-147">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="4d900-148">在所有连接的客户端上调用的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-148">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="4d900-149">在客户端调用集线器方法调用的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-149">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="4d900-150">除调用该方法的客户端的所有已连接客户端上调用的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-150">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="4d900-151">`Hub.Clients` 此外包含以下方法：</span><span class="sxs-lookup"><span data-stu-id="4d900-151">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="4d900-152">方法</span><span class="sxs-lookup"><span data-stu-id="4d900-152">Method</span></span> | <span data-ttu-id="4d900-153">描述</span><span class="sxs-lookup"><span data-stu-id="4d900-153">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="4d900-154">除了指定连接的所有已连接客户端上调用的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-154">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="4d900-155">调用特定连接的客户端上的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-155">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="4d900-156">调用特定连接的客户端上的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-156">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="4d900-157">指定的组中的所有连接上调用的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-157">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="4d900-158">在指定组中，指定的连接除外的所有连接上调用的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-158">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="4d900-159">在多个组的连接上调用的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-159">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="4d900-160">上一组连接，不包括客户端调用集线器方法调用的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-160">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="4d900-161">与特定用户关联的所有连接上调用的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-161">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="4d900-162">使用指定的用户关联的所有连接上调用的方法</span><span class="sxs-lookup"><span data-stu-id="4d900-162">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="4d900-163">每个属性或方法上表中的返回一个包含对象`SendAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="4d900-163">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="4d900-164">`SendAsync`方法允许您提供的名称和要调用的客户端方法的参数。</span><span class="sxs-lookup"><span data-stu-id="4d900-164">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="4d900-165">将消息发送到客户端</span><span class="sxs-lookup"><span data-stu-id="4d900-165">Send messages to clients</span></span>

<span data-ttu-id="4d900-166">若要使对特定的客户端的调用，使用的属性`Clients`对象。</span><span class="sxs-lookup"><span data-stu-id="4d900-166">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="4d900-167">在以下示例中，有三个中心的方法：</span><span class="sxs-lookup"><span data-stu-id="4d900-167">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="4d900-168">`SendMessage` 将消息发送到所有已连接客户端，使用`Clients.All`。</span><span class="sxs-lookup"><span data-stu-id="4d900-168">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="4d900-169">`SendMessageToCaller` 将消息发送回调用方，使用`Clients.Caller`。</span><span class="sxs-lookup"><span data-stu-id="4d900-169">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="4d900-170">`SendMessageToGroups` 将消息发送到中的所有客户端`SignalR Users`组。</span><span class="sxs-lookup"><span data-stu-id="4d900-170">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="4d900-171">强类型化的中心</span><span class="sxs-lookup"><span data-stu-id="4d900-171">Strongly typed hubs</span></span>

<span data-ttu-id="4d900-172">使用的一个缺点`SendAsync`是依赖于使用魔幻字符串来指定客户端方法调用。</span><span class="sxs-lookup"><span data-stu-id="4d900-172">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="4d900-173">这将使运行时错误，如果方法名称的拼写错误的代码打开或缺少从客户端。</span><span class="sxs-lookup"><span data-stu-id="4d900-173">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="4d900-174">使用的替代方法`SendAsync`是强类型化`Hub`与<xref:Microsoft.AspNetCore.SignalR.Hub%601>。</span><span class="sxs-lookup"><span data-stu-id="4d900-174">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="4d900-175">在以下示例中，`ChatHub`客户端方法具有出提取到一个接口，称为`IChatClient`。</span><span class="sxs-lookup"><span data-stu-id="4d900-175">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>  

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="4d900-176">此接口可用于重构前面`ChatHub`示例。</span><span class="sxs-lookup"><span data-stu-id="4d900-176">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="4d900-177">使用`Hub<IChatClient>`启用编译时检查的客户端方法。</span><span class="sxs-lookup"><span data-stu-id="4d900-177">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="4d900-178">这可以防止由于使用魔幻字符串导致的问题`Hub<T>`可以仅提供访问权限在接口中定义的方法。</span><span class="sxs-lookup"><span data-stu-id="4d900-178">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="4d900-179">使用强类型化`Hub<T>`禁用的功能使用`SendAsync`。</span><span class="sxs-lookup"><span data-stu-id="4d900-179">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="4d900-180">该接口上定义的任何方法仍可以定义为异步。</span><span class="sxs-lookup"><span data-stu-id="4d900-180">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="4d900-181">实际上，每种方法应返回`Task`。</span><span class="sxs-lookup"><span data-stu-id="4d900-181">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="4d900-182">由于它是一个接口，不要使用`async`关键字。</span><span class="sxs-lookup"><span data-stu-id="4d900-182">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="4d900-183">例如：</span><span class="sxs-lookup"><span data-stu-id="4d900-183">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="4d900-184">`Async`后缀不从方法名称中去除。</span><span class="sxs-lookup"><span data-stu-id="4d900-184">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="4d900-185">除非客户端方法使用定义`.on('MyMethodAsync')`，则不应使用`MyMethodAsync`作为名称。</span><span class="sxs-lookup"><span data-stu-id="4d900-185">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="4d900-186">将集线器方法的名称更改</span><span class="sxs-lookup"><span data-stu-id="4d900-186">Change the name of a hub method</span></span>

<span data-ttu-id="4d900-187">默认情况下，服务器中心方法名称为.NET 方法的名称。</span><span class="sxs-lookup"><span data-stu-id="4d900-187">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="4d900-188">但是，可以使用[HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute)属性来更改此默认设置，并手动指定方法的名称。</span><span class="sxs-lookup"><span data-stu-id="4d900-188">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="4d900-189">调用该方法时，客户端应而不是.NET 方法名称，使用此名称。</span><span class="sxs-lookup"><span data-stu-id="4d900-189">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="4d900-190">处理连接事件</span><span class="sxs-lookup"><span data-stu-id="4d900-190">Handle events for a connection</span></span>

<span data-ttu-id="4d900-191">SignalR 中心 API 提供了`OnConnectedAsync`和`OnDisconnectedAsync`管理和跟踪连接的虚拟方法。</span><span class="sxs-lookup"><span data-stu-id="4d900-191">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="4d900-192">重写`OnConnectedAsync`虚拟方法，客户端连接到中心，如将其添加到组时，执行操作。</span><span class="sxs-lookup"><span data-stu-id="4d900-192">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="4d900-193">重写`OnDisconnectedAsync`虚拟方法，当客户端断开连接时执行的操作。</span><span class="sxs-lookup"><span data-stu-id="4d900-193">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="4d900-194">如果客户端有意断开连接 (通过调用`connection.stop()`，例如)，则`exception`参数将为`null`。</span><span class="sxs-lookup"><span data-stu-id="4d900-194">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="4d900-195">但是，如果客户端已断开连接错误 （例如网络故障），由于`exception`参数将包含描述失败的异常。</span><span class="sxs-lookup"><span data-stu-id="4d900-195">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a><span data-ttu-id="4d900-196">处理错误</span><span class="sxs-lookup"><span data-stu-id="4d900-196">Handle errors</span></span>

<span data-ttu-id="4d900-197">集线器方法中引发的异常将发送到的客户端的调用的方法。</span><span class="sxs-lookup"><span data-stu-id="4d900-197">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="4d900-198">在 JavaScript 客户端`invoke`方法将返回[JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)。</span><span class="sxs-lookup"><span data-stu-id="4d900-198">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="4d900-199">当客户端收到的错误处理程序附加到承诺使用`catch`，它具有调用，并且作为 JavaScript 传递`Error`对象。</span><span class="sxs-lookup"><span data-stu-id="4d900-199">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="4d900-200">如果你的中心引发了异常，不会关闭连接。</span><span class="sxs-lookup"><span data-stu-id="4d900-200">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="4d900-201">默认情况下，SignalR 返回到客户端的一般性错误消息。</span><span class="sxs-lookup"><span data-stu-id="4d900-201">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="4d900-202">例如：</span><span class="sxs-lookup"><span data-stu-id="4d900-202">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="4d900-203">意外的异常通常包含敏感信息，例如触发数据库连接失败时引发异常的数据库服务器的名称。</span><span class="sxs-lookup"><span data-stu-id="4d900-203">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="4d900-204">SignalR 并不作为一种安全措施，默认情况下公开这些详细的错误消息。</span><span class="sxs-lookup"><span data-stu-id="4d900-204">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="4d900-205">请参阅[安全注意事项文章](xref:signalr/security#exceptions)异常详细信息所抑制的原因的详细信息。</span><span class="sxs-lookup"><span data-stu-id="4d900-205">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="4d900-206">如果有异常条件您*做*想要传播到客户端，则可以使用`HubException`类。</span><span class="sxs-lookup"><span data-stu-id="4d900-206">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="4d900-207">如果引发`HubException`从中心方法，SignalR**将**将整个消息发送到客户端，以未经修改的。</span><span class="sxs-lookup"><span data-stu-id="4d900-207">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="4d900-208">SignalR 只发送`Message`到客户端异常的属性。</span><span class="sxs-lookup"><span data-stu-id="4d900-208">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="4d900-209">堆栈跟踪和异常上的其他属性不是可用于客户端。</span><span class="sxs-lookup"><span data-stu-id="4d900-209">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="4d900-210">相关资源</span><span class="sxs-lookup"><span data-stu-id="4d900-210">Related resources</span></span>

* [<span data-ttu-id="4d900-211">ASP.NET Core SignalR 简介</span><span class="sxs-lookup"><span data-stu-id="4d900-211">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="4d900-212">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="4d900-212">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="4d900-213">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="4d900-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
