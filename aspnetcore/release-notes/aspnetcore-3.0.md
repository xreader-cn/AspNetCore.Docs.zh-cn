---
title: ASP.NET Core 3.0 的新增功能
author: rick-anderson
description: 了解 ASP.NET Core 3.0 的新增功能。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 10741cfc701bf261484b9e6c73a2347e2dd7083d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019829"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="8d57d-103">ASP.NET Core 3.0 的新增功能</span><span class="sxs-lookup"><span data-stu-id="8d57d-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="8d57d-104">本文重点介绍 ASP.NET Core 3.0 中最重要的更改，并提供相关文档的链接。</span><span class="sxs-lookup"><span data-stu-id="8d57d-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

<span data-ttu-id="8d57d-105">Blazor 是 ASP.NET Core 中的新框架，用于使用 .NET 生成交互式客户端 Web UI：</span><span class="sxs-lookup"><span data-stu-id="8d57d-105">Blazor is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="8d57d-106">使用 C# 代替 JavaScript 来创建丰富的交互式 UI。</span><span class="sxs-lookup"><span data-stu-id="8d57d-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="8d57d-107">共享使用 .NET 编写的服务器端和客户端应用逻辑。</span><span class="sxs-lookup"><span data-stu-id="8d57d-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="8d57d-108">将 UI 呈现为 HTML 和 CSS，以支持众多浏览器，其中包括移动浏览器。</span><span class="sxs-lookup"><span data-stu-id="8d57d-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="8d57d-109">Blazor 框架支持的方案：</span><span class="sxs-lookup"><span data-stu-id="8d57d-109">Blazor framework supported scenarios:</span></span>

* <span data-ttu-id="8d57d-110">可重用的 UI 组件（Razor 组件）</span><span class="sxs-lookup"><span data-stu-id="8d57d-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="8d57d-111">客户端路由</span><span class="sxs-lookup"><span data-stu-id="8d57d-111">Client-side routing</span></span>
* <span data-ttu-id="8d57d-112">组件布局</span><span class="sxs-lookup"><span data-stu-id="8d57d-112">Component layouts</span></span>
* <span data-ttu-id="8d57d-113">对依赖项注入的支持</span><span class="sxs-lookup"><span data-stu-id="8d57d-113">Support for dependency injection</span></span>
* <span data-ttu-id="8d57d-114">窗体和验证</span><span class="sxs-lookup"><span data-stu-id="8d57d-114">Forms and validation</span></span>
* <span data-ttu-id="8d57d-115">用 Razor 类库构建组件库</span><span class="sxs-lookup"><span data-stu-id="8d57d-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="8d57d-116">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="8d57d-116">JavaScript interop</span></span>

<span data-ttu-id="8d57d-117">有关详细信息，请参阅 <xref:blazor/index>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-117">For more information, see <xref:blazor/index>.</span></span>

### Blazor Server

<span data-ttu-id="8d57d-118">Blazor 将组件呈现逻辑从 UI 更新的应用方式中分离出来。</span><span class="sxs-lookup"><span data-stu-id="8d57d-118">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="8d57d-119">Blazor Server在 ASP.NET Core 应用中支持在服务器上托管 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="8d57d-119">Blazor Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="8d57d-120">可通过 SignalR 连接处理 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="8d57d-120">UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="8d57d-121">ASP.NET Core 3.0 支持 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="8d57d-121">Blazor Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="no-locblazor-webassembly-preview"></a><span data-ttu-id="8d57d-122">Blazor WebAssembly（预览版）</span><span class="sxs-lookup"><span data-stu-id="8d57d-122">Blazor WebAssembly (Preview)</span></span>

<span data-ttu-id="8d57d-123">还可以使用基于 WebAssembly 的 .NET 运行时直接在浏览器中运行 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="8d57d-123">Blazor apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="8d57d-124">Blazor WebAssembly 处于预览版阶段，ASP.NET Core 3.0 不提供支持。</span><span class="sxs-lookup"><span data-stu-id="8d57d-124">Blazor WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8d57d-125">ASP.NET Core 的未来版本将支持 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="8d57d-125">Blazor WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="no-locrazor-components"></a><span data-ttu-id="8d57d-126">Razor 组件</span><span class="sxs-lookup"><span data-stu-id="8d57d-126">Razor components</span></span>

<span data-ttu-id="8d57d-127">Blazor 应用是基于组件构建的。</span><span class="sxs-lookup"><span data-stu-id="8d57d-127">Blazor apps are built from components.</span></span> <span data-ttu-id="8d57d-128">组件是自包含的用户界面 (UI) 块，例如页、对话框或窗体。</span><span class="sxs-lookup"><span data-stu-id="8d57d-128">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="8d57d-129">组件是定义 UI 呈现逻辑和客户端事件处理程序的普通 .NET 类。</span><span class="sxs-lookup"><span data-stu-id="8d57d-129">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="8d57d-130">无需 JavaScript 即可创建丰富的交互式 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="8d57d-130">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="8d57d-131">通常使用 Razor 语法（HTML 和 C# 的自然混合）创建 Blazor 中的组件。</span><span class="sxs-lookup"><span data-stu-id="8d57d-131">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="8d57d-132">Razor 组件与 Razor Pages 和 MVC 视图类似，因为它们都使用 Razor。</span><span class="sxs-lookup"><span data-stu-id="8d57d-132">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="8d57d-133">与基于请求-响应模型的页和视图不同，组件专门用于处理 UI 构成。</span><span class="sxs-lookup"><span data-stu-id="8d57d-133">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="8d57d-134">gRPC</span><span class="sxs-lookup"><span data-stu-id="8d57d-134">gRPC</span></span>

<span data-ttu-id="8d57d-135">[gRPC](https://grpc.io/)：</span><span class="sxs-lookup"><span data-stu-id="8d57d-135">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="8d57d-136">是一个流行的高性能 RPC（远程过程调用）框架。</span><span class="sxs-lookup"><span data-stu-id="8d57d-136">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="8d57d-137">提供固定的协定优先方法进行 API 开发。</span><span class="sxs-lookup"><span data-stu-id="8d57d-137">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="8d57d-138">使用的新式技术如下：</span><span class="sxs-lookup"><span data-stu-id="8d57d-138">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="8d57d-139">用于传输的 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="8d57d-139">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="8d57d-140">作为接口描述语言的协议缓冲区。</span><span class="sxs-lookup"><span data-stu-id="8d57d-140">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="8d57d-141">二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="8d57d-141">Binary serialization format.</span></span>
* <span data-ttu-id="8d57d-142">提供如下功能：</span><span class="sxs-lookup"><span data-stu-id="8d57d-142">Provides features such as:</span></span>

  * <span data-ttu-id="8d57d-143">身份验证</span><span class="sxs-lookup"><span data-stu-id="8d57d-143">Authentication</span></span>
  * <span data-ttu-id="8d57d-144">双向流式处理和流控制。</span><span class="sxs-lookup"><span data-stu-id="8d57d-144">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="8d57d-145">取消和超时。</span><span class="sxs-lookup"><span data-stu-id="8d57d-145">Cancellation and timeouts.</span></span>

<span data-ttu-id="8d57d-146">ASP.NET Core 3.0 中的 gRPC 功能包括：</span><span class="sxs-lookup"><span data-stu-id="8d57d-146">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="8d57d-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore)：用于托管 gRPC 服务的 ASP.NET Core 框架。</span><span class="sxs-lookup"><span data-stu-id="8d57d-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="8d57d-148">ASP.NET Core 上的 gRPC 与标准 ASP.NET Core 功能（例如日志记录、依赖关系注入 (DI)、身份验证和授权）集成。</span><span class="sxs-lookup"><span data-stu-id="8d57d-148">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="8d57d-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)：基于熟悉的 `HttpClient` 构建的 .NET Core 的 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="8d57d-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="8d57d-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory)：gRPC 客户端与 `HttpClientFactory` 的集成。</span><span class="sxs-lookup"><span data-stu-id="8d57d-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="8d57d-151">有关详细信息，请参阅 <xref:grpc/index>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-151">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="8d57d-152">有关迁移说明，请参阅[更新 ](xref:migration/22-to-30#signalr) 代码SignalR。</span><span class="sxs-lookup"><span data-stu-id="8d57d-152">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="8d57d-153">SignalR 现在使用 `System.Text.Json` 来序列化/反序列化 JSON 消息。</span><span class="sxs-lookup"><span data-stu-id="8d57d-153">SignalR now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="8d57d-154">有关还原基于 `Newtonsoft.Json` 的序列化程序的说明，请参阅[切换到 Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-154">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="8d57d-155">在 SignalR 的 JavaScript 和 .NET 客户端中，添加了自动重新连接支持。</span><span class="sxs-lookup"><span data-stu-id="8d57d-155">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="8d57d-156">默认情况下，客户端会立即尝试重新连接，并根据需要分别在 2 秒、10 秒和 30 秒后重试。</span><span class="sxs-lookup"><span data-stu-id="8d57d-156">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="8d57d-157">如果客户端成功重新连接，则会收到新的连接 ID。</span><span class="sxs-lookup"><span data-stu-id="8d57d-157">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="8d57d-158">选择启用自动重新连接：</span><span class="sxs-lookup"><span data-stu-id="8d57d-158">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="8d57d-159">可以通过传递基于毫秒的持续时间数组来指定重新连接间隔：</span><span class="sxs-lookup"><span data-stu-id="8d57d-159">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="8d57d-160">可以通过传入自定义实现来完全控制重新连接间隔。</span><span class="sxs-lookup"><span data-stu-id="8d57d-160">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="8d57d-161">如果在上一次重新连接间隔后重新连接失败：</span><span class="sxs-lookup"><span data-stu-id="8d57d-161">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="8d57d-162">客户端认为连接处于脱机状态。</span><span class="sxs-lookup"><span data-stu-id="8d57d-162">The client considers the connection is offline.</span></span>
* <span data-ttu-id="8d57d-163">客户端将停止尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="8d57d-163">The client stops trying to reconnect.</span></span>

<span data-ttu-id="8d57d-164">尝试重新连接时，更新应用 UI，通知用户正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="8d57d-164">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="8d57d-165">为了在连接中断时提供 UI 反馈，SignalR 客户端 API 已扩展为包含以下事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="8d57d-165">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="8d57d-166">`onreconnecting`：使开发人员有机会禁用 UI 或允许用户了解应用程序处于脱机状态。</span><span class="sxs-lookup"><span data-stu-id="8d57d-166">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="8d57d-167">`onreconnected`：使开发人员有机会在重新建立连接后更新 UI。</span><span class="sxs-lookup"><span data-stu-id="8d57d-167">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="8d57d-168">以下代码在尝试连接时使用 `onreconnecting` 更新 UI：</span><span class="sxs-lookup"><span data-stu-id="8d57d-168">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="8d57d-169">以下代码在连接时使用 `onreconnected` 更新 UI：</span><span class="sxs-lookup"><span data-stu-id="8d57d-169">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="8d57d-170">当中心方法要求授权时，SignalR 3.0 和更高版本为授权处理程序提供了一个自定义资源。</span><span class="sxs-lookup"><span data-stu-id="8d57d-170">SignalR 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="8d57d-171">资源是 `HubInvocationContext` 的一个实例。</span><span class="sxs-lookup"><span data-stu-id="8d57d-171">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="8d57d-172">`HubInvocationContext` 包括：</span><span class="sxs-lookup"><span data-stu-id="8d57d-172">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="8d57d-173">正在调用的中心方法的名称。</span><span class="sxs-lookup"><span data-stu-id="8d57d-173">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="8d57d-174">中心方法的参数。</span><span class="sxs-lookup"><span data-stu-id="8d57d-174">Arguments to the hub method.</span></span>

<span data-ttu-id="8d57d-175">请考虑以下通过 Azure Active Directory 允许多个组织登录的聊天室应用示例。</span><span class="sxs-lookup"><span data-stu-id="8d57d-175">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="8d57d-176">拥有 Microsoft 帐户的任何人都可以登录到聊天，但只有所属组织的成员才能阻止用户或查看用户的聊天历史记录。</span><span class="sxs-lookup"><span data-stu-id="8d57d-176">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="8d57d-177">该应用可以限制特定用户的某些功能。</span><span class="sxs-lookup"><span data-stu-id="8d57d-177">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="8d57d-178">在前面的代码中，`DomainRestrictedRequirement` 作为自定义 `IAuthorizationRequirement`。</span><span class="sxs-lookup"><span data-stu-id="8d57d-178">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="8d57d-179">由于正在传入 `HubInvocationContext` 资源参数，因此内部逻辑可以：</span><span class="sxs-lookup"><span data-stu-id="8d57d-179">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="8d57d-180">检查在其中调用中心的上下文。</span><span class="sxs-lookup"><span data-stu-id="8d57d-180">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="8d57d-181">做出允许用户执行单个中心方法的决策。</span><span class="sxs-lookup"><span data-stu-id="8d57d-181">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="8d57d-182">可以通过代码在运行时检查的策略的名称来标记单个中心方法。</span><span class="sxs-lookup"><span data-stu-id="8d57d-182">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="8d57d-183">当客户端尝试调用单个中心方法时，`DomainRestrictedRequirement` 处理程序将运行并控制对方法的访问。</span><span class="sxs-lookup"><span data-stu-id="8d57d-183">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="8d57d-184">基于 `DomainRestrictedRequirement` 控制访问的方式：</span><span class="sxs-lookup"><span data-stu-id="8d57d-184">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="8d57d-185">所有已登录的用户都可以调用 `SendMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="8d57d-185">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="8d57d-186">只有使用 `@jabbr.net` 电子邮件地址登录的用户才能查看用户的历史记录。</span><span class="sxs-lookup"><span data-stu-id="8d57d-186">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="8d57d-187">只有 `bob42@jabbr.net` 才能阻止用户进入聊天室。</span><span class="sxs-lookup"><span data-stu-id="8d57d-187">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="8d57d-188">创建 `DomainRestricted` 策略可能涉及：</span><span class="sxs-lookup"><span data-stu-id="8d57d-188">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="8d57d-189">在 Startup.cs 中，添加新策略。</span><span class="sxs-lookup"><span data-stu-id="8d57d-189">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="8d57d-190">提供自定义 `DomainRestrictedRequirement` 要求作为参数。</span><span class="sxs-lookup"><span data-stu-id="8d57d-190">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="8d57d-191">向授权中间件注册 `DomainRestricted`。</span><span class="sxs-lookup"><span data-stu-id="8d57d-191">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="8d57d-192">SignalR 中心使用[终结点路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-192">SignalR hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="8d57d-193">以前 SignalR 中心连接是显式完成的：</span><span class="sxs-lookup"><span data-stu-id="8d57d-193">SignalR hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="8d57d-194">在以前的版本中，开发人员需要将控制器、Razor 页面和中心连接到各种位置。</span><span class="sxs-lookup"><span data-stu-id="8d57d-194">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="8d57d-195">显式连接会生成一系列几乎相同的路由段：</span><span class="sxs-lookup"><span data-stu-id="8d57d-195">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

<span data-ttu-id="8d57d-196">可以通过终结点路由来路由 SignalR 3.0 中心。</span><span class="sxs-lookup"><span data-stu-id="8d57d-196">SignalR 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="8d57d-197">通过终结点路由，通常可以在 `UseRouting` 中配置所有路由：</span><span class="sxs-lookup"><span data-stu-id="8d57d-197">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="8d57d-198">ASP.NET Core 3.0 SignalR 添加了：</span><span class="sxs-lookup"><span data-stu-id="8d57d-198">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="8d57d-199">客户端到服务器的流式处理。</span><span class="sxs-lookup"><span data-stu-id="8d57d-199">Client-to-server streaming.</span></span> <span data-ttu-id="8d57d-200">使用客户端到服务器的流式处理时，服务器端方法可以获取 `IAsyncEnumerable<T>` 或 `ChannelReader<T>` 的实例。</span><span class="sxs-lookup"><span data-stu-id="8d57d-200">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="8d57d-201">在下面的 C# 示例中，中心上的 `UploadStream` 方法将从客户端接收字符串流：</span><span class="sxs-lookup"><span data-stu-id="8d57d-201">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="8d57d-202">.NET 客户端应用程序可以将 `IAsyncEnumerable<T>` 或 `ChannelReader<T>` 实例作为上述 `UploadStream` 中心方法的 `stream` 参数传递。</span><span class="sxs-lookup"><span data-stu-id="8d57d-202">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="8d57d-203">`for` 循环完成并且本地函数退出后，将发送流完成：</span><span class="sxs-lookup"><span data-stu-id="8d57d-203">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="8d57d-204">JavaScript 客户端应用将 SignalR `Subject`（或 [RxJS 主题](https://rxjs.dev/api/index/class/Subject)）用于上面的 `UploadStream` 中心方法的 `stream` 参数。</span><span class="sxs-lookup"><span data-stu-id="8d57d-204">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="8d57d-205">当字符串被捕获并准备发送给服务器时，JavaScript 代码可以使用 `subject.next` 方法处理字符串。</span><span class="sxs-lookup"><span data-stu-id="8d57d-205">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="8d57d-206">使用类似于上述两个代码片段的代码，可创建实时流式处理体验。</span><span class="sxs-lookup"><span data-stu-id="8d57d-206">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="8d57d-207">新的 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="8d57d-207">New JSON serialization</span></span>

<span data-ttu-id="8d57d-208">ASP.NET Core 3.0 现在默认使用 <xref:System.Text.Json> 进行 JSON 序列化：</span><span class="sxs-lookup"><span data-stu-id="8d57d-208">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="8d57d-209">以异步方式读取和写入 JSON。</span><span class="sxs-lookup"><span data-stu-id="8d57d-209">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="8d57d-210">针对 UTF-8 文本进行了优化。</span><span class="sxs-lookup"><span data-stu-id="8d57d-210">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="8d57d-211">通常比 `Newtonsoft.Json` 性能更高。</span><span class="sxs-lookup"><span data-stu-id="8d57d-211">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="8d57d-212">若要将 Json.NET 添加到 ASP.NET Core 3.0，请参阅[添加基于 Newtonsoft.Json 的 JSON 格式支持](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-212">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-no-locrazor-directives"></a><span data-ttu-id="8d57d-213">新的 Razor 指令</span><span class="sxs-lookup"><span data-stu-id="8d57d-213">New Razor directives</span></span>

<span data-ttu-id="8d57d-214">下面的列表包含新的 Razor 指令：</span><span class="sxs-lookup"><span data-stu-id="8d57d-214">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="8d57d-215">[`@attribute`](xref:mvc/views/razor#attribute)：`@attribute` 指令将给定的属性应用于生成的页或视图的类。</span><span class="sxs-lookup"><span data-stu-id="8d57d-215">[`@attribute`](xref:mvc/views/razor#attribute): The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="8d57d-216">例如 `@attribute [Authorize]`。</span><span class="sxs-lookup"><span data-stu-id="8d57d-216">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="8d57d-217">[`@implements`](xref:mvc/views/razor#implements)：`@implements` 指令为生成的类实现接口。</span><span class="sxs-lookup"><span data-stu-id="8d57d-217">[`@implements`](xref:mvc/views/razor#implements): The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="8d57d-218">例如 `@implements IDisposable`。</span><span class="sxs-lookup"><span data-stu-id="8d57d-218">For example, `@implements IDisposable`.</span></span>

## <a name="no-locidentityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="8d57d-219">IdentityServer4 支持 Web API 和 SPA 的身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="8d57d-219">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="8d57d-220">ASP.NET Core 3.0 使用 Web API 授权的支持在单页应用 (SPA) 中提供身份验证。</span><span class="sxs-lookup"><span data-stu-id="8d57d-220">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="8d57d-221">用于验证和存储用户身份信息的 ASP.NET Core Identity 与用于实现 Open ID Connect 的 [IdentityServer4](https://identityserver.io/) 结合使用。</span><span class="sxs-lookup"><span data-stu-id="8d57d-221">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="8d57d-222">IdentityServer4 是适用于 ASP.NET Core 3.0 的 OpenID Connect 和 OAuth 2.0 框架。</span><span class="sxs-lookup"><span data-stu-id="8d57d-222">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="8d57d-223">它提供了以下安全功能：</span><span class="sxs-lookup"><span data-stu-id="8d57d-223">It enables the following security features:</span></span>

* <span data-ttu-id="8d57d-224">身份验证即服务 (AaaS)</span><span class="sxs-lookup"><span data-stu-id="8d57d-224">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="8d57d-225">跨多个应用程序类型的单一登录/注销 (SSO)</span><span class="sxs-lookup"><span data-stu-id="8d57d-225">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="8d57d-226">API 的访问控制</span><span class="sxs-lookup"><span data-stu-id="8d57d-226">Access control for APIs</span></span>
* <span data-ttu-id="8d57d-227">Federation Gateway</span><span class="sxs-lookup"><span data-stu-id="8d57d-227">Federation Gateway</span></span>

<span data-ttu-id="8d57d-228">有关详细信息，请参阅 [IdentityServer4 文档](http://docs.identityserver.io/en/latest/index.html)或 [SPA 的身份验证和授权](xref:security/authentication/identity/spa)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-228">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="8d57d-229">证书和 Kerberos 身份验证</span><span class="sxs-lookup"><span data-stu-id="8d57d-229">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="8d57d-230">证书身份验证需要：</span><span class="sxs-lookup"><span data-stu-id="8d57d-230">Certificate authentication requires:</span></span>

* <span data-ttu-id="8d57d-231">将服务器配置为接受证书。</span><span class="sxs-lookup"><span data-stu-id="8d57d-231">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="8d57d-232">在 `Startup.Configure` 中添加身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="8d57d-232">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="8d57d-233">将证书身份验证服务添加到 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="8d57d-233">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="8d57d-234">用于证书身份验证的选项包括执行以下操作的能力：</span><span class="sxs-lookup"><span data-stu-id="8d57d-234">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="8d57d-235">接受自签名证书。</span><span class="sxs-lookup"><span data-stu-id="8d57d-235">Accept self-signed certificates.</span></span>
* <span data-ttu-id="8d57d-236">检查证书吊销。</span><span class="sxs-lookup"><span data-stu-id="8d57d-236">Check for certificate revocation.</span></span>
* <span data-ttu-id="8d57d-237">检查提供证书中是否有正确的使用标志。</span><span class="sxs-lookup"><span data-stu-id="8d57d-237">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="8d57d-238">默认的用户主体是从证书属性构造的。</span><span class="sxs-lookup"><span data-stu-id="8d57d-238">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="8d57d-239">用户主体包含允许补充或替换主体的事件。</span><span class="sxs-lookup"><span data-stu-id="8d57d-239">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="8d57d-240">有关详细信息，请参阅 <xref:security/authentication/certauth>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-240">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="8d57d-241">[Windows 身份验证](/windows-server/security/windows-authentication/windows-authentication-overview)已扩展到 Linux 和 macOS。</span><span class="sxs-lookup"><span data-stu-id="8d57d-241">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="8d57d-242">在以前的版本中，Windows 身份验证限制为 [IIS](xref:host-and-deploy/iis/index) 和 [HttpSys](xref:fundamentals/servers/httpsys)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-242">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="8d57d-243">在 ASP.NET Core 3.0 中，[Kestrel](xref:fundamentals/servers/kestrel) 能够在 Windows、Linux 和 macOS 上对已加入 Windows 域的主机使用 Negotiate、[Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview) 和 [NTLM](/windows-server/security/kerberos/ntlm-overview)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-243">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="8d57d-244">Kestrel 对这些身份验证方案的支持由 [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 包提供。</span><span class="sxs-lookup"><span data-stu-id="8d57d-244">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="8d57d-245">对于其他身份验证服务，请配置身份验证应用范围，然后配置服务：</span><span class="sxs-lookup"><span data-stu-id="8d57d-245">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="8d57d-246">主机要求：</span><span class="sxs-lookup"><span data-stu-id="8d57d-246">Host requirements:</span></span>

* <span data-ttu-id="8d57d-247">Windows 主机必须将[主体名称](/windows/win32/ad/service-principal-names) (SPN) 添加到托管应用的用户帐户。</span><span class="sxs-lookup"><span data-stu-id="8d57d-247">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="8d57d-248">Linux 和 macOS 计算机必须加入域。</span><span class="sxs-lookup"><span data-stu-id="8d57d-248">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="8d57d-249">必须为 Web 进程创建 SPN。</span><span class="sxs-lookup"><span data-stu-id="8d57d-249">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="8d57d-250">必须在主机上生成并配置 [Keytab 文件](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-250">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="8d57d-251">有关详细信息，请参阅 <xref:security/authentication/windowsauth>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-251">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="8d57d-252">模板更改</span><span class="sxs-lookup"><span data-stu-id="8d57d-252">Template changes</span></span>

<span data-ttu-id="8d57d-253">Web UI 模板（Razor Pages、具有控制器和视图的 MVC）已删除以下内容：</span><span class="sxs-lookup"><span data-stu-id="8d57d-253">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="8d57d-254">cookie 同意 UI 不再包括在内。</span><span class="sxs-lookup"><span data-stu-id="8d57d-254">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="8d57d-255">若要在 ASP.NET Core 3.0 模板生成的应用中启用 cookie 同意功能，请参阅 <xref:security/gdpr>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-255">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="8d57d-256">脚本和相关静态资产现在作为本地文件（而不是使用 CDN）进行引用。</span><span class="sxs-lookup"><span data-stu-id="8d57d-256">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="8d57d-257">有关详细信息，[脚本和相关静态资产现在基于当前环境作为本地文件（而不是使用 CDN）进行引用 (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-257">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="8d57d-258">Angular 模板已更新，以便使用 Angular 8。</span><span class="sxs-lookup"><span data-stu-id="8d57d-258">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="8d57d-259">默认情况下，Razor 类库 (RCL) 模板默认为 Razor 组件开发。</span><span class="sxs-lookup"><span data-stu-id="8d57d-259">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="8d57d-260">Visual Studio 中的新模板选项提供了对页面和视图的模板支持。</span><span class="sxs-lookup"><span data-stu-id="8d57d-260">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="8d57d-261">在命令行界面中通过模板创建 RCL 时，请传递 `--support-pages-and-views` 选项 (`dotnet new razorclasslib --support-pages-and-views`)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-261">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="8d57d-262">泛型主机</span><span class="sxs-lookup"><span data-stu-id="8d57d-262">Generic Host</span></span>

<span data-ttu-id="8d57d-263">ASP.NET Core 3.0 模板使用 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-263">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="8d57d-264">以前版本使用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-264">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="8d57d-265">使用 .NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>) 可以更好地将 ASP.NET Core 应用与非 Web 特定的其他服务器方案集成。</span><span class="sxs-lookup"><span data-stu-id="8d57d-265">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="8d57d-266">有关详细信息，请参阅 [HostBuilder 替换 WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-266">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="8d57d-267">主机配置</span><span class="sxs-lookup"><span data-stu-id="8d57d-267">Host configuration</span></span>

<span data-ttu-id="8d57d-268">在 ASP.NET Core 3.0 版本之前，为 Web 主机的主机配置加载了前缀为 `ASPNETCORE_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8d57d-268">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="8d57d-269">在 3.0 中，对于带有 `CreateDefaultBuilder` 的主机配置，使用 `AddEnvironmentVariables` 加载以 `DOTNET_` 为前缀的环境变量。</span><span class="sxs-lookup"><span data-stu-id="8d57d-269">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="8d57d-270">对启动构造函数注入的更改</span><span class="sxs-lookup"><span data-stu-id="8d57d-270">Changes to Startup constructor injection</span></span>

<span data-ttu-id="8d57d-271">泛型主机仅支持以下类型的 `Startup` 构造函数注入：</span><span class="sxs-lookup"><span data-stu-id="8d57d-271">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="8d57d-272">所有服务仍可以直接作为 `Startup.Configure` 方法的参数注入。</span><span class="sxs-lookup"><span data-stu-id="8d57d-272">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="8d57d-273">有关详细信息，请参阅[泛型主机限制启动构造函数注入 (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-273">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="8d57d-274">Kestrel</span><span class="sxs-lookup"><span data-stu-id="8d57d-274">Kestrel</span></span>

* <span data-ttu-id="8d57d-275">已更新 Kestrel 配置以迁移到泛型主机。</span><span class="sxs-lookup"><span data-stu-id="8d57d-275">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="8d57d-276">在 3.0 中，Kestrel 是在 `ConfigureWebHostDefaults` 提供的 Web 主机生成器上配置的。</span><span class="sxs-lookup"><span data-stu-id="8d57d-276">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="8d57d-277">已从 Kestrel 中删除连接适配器，并将其替换为连接中间件，这与 ASP.NET Core 管道中的 HTTP 中间件类似，但适用于较低级别的连接。</span><span class="sxs-lookup"><span data-stu-id="8d57d-277">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="8d57d-278">Kestrel 传输层已作为 `Connections.Abstractions` 中的公共接口公开。</span><span class="sxs-lookup"><span data-stu-id="8d57d-278">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="8d57d-279">通过将尾随标题移到新集合中，已解决了标头和尾部之间的歧义。</span><span class="sxs-lookup"><span data-stu-id="8d57d-279">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="8d57d-280">线程不足会导致应用崩溃，而同步 I/O API（例如 `HttpRequest.Body.Read`）是导致线程不足的常见原因。</span><span class="sxs-lookup"><span data-stu-id="8d57d-280">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="8d57d-281">在 3.0 中，默认情况下禁用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="8d57d-281">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="8d57d-282">有关详细信息，请参阅 <xref:migration/22-to-30#kestrel>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-282">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="8d57d-283">默认情况下启用 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8d57d-283">HTTP/2 enabled by default</span></span>

<span data-ttu-id="8d57d-284">默认情况下，在 HTTPS 终结点的 Kestrel 中启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="8d57d-284">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="8d57d-285">当操作系统支持 HTTP/2 时，对 IIS 或 HTTP.sys 启用 HTTP/2 支持。</span><span class="sxs-lookup"><span data-stu-id="8d57d-285">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="8d57d-286">根据请求提供事件计数器</span><span class="sxs-lookup"><span data-stu-id="8d57d-286">EventCounters on request</span></span>

<span data-ttu-id="8d57d-287">托管事件源 `Microsoft.AspNetCore.Hosting` 发出以下与传入请求有关的新 <xref:System.Diagnostics.Tracing.EventCounter> 类型：</span><span class="sxs-lookup"><span data-stu-id="8d57d-287">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="8d57d-288">终结点路由</span><span class="sxs-lookup"><span data-stu-id="8d57d-288">Endpoint routing</span></span>

<span data-ttu-id="8d57d-289">增强了终结点路由，终结点路由可以让框架（例如 MVC）与中间件配合使用：</span><span class="sxs-lookup"><span data-stu-id="8d57d-289">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="8d57d-290">中间件和终结点的顺序可在 `Startup.Configure` 的请求处理管道中进行配置。</span><span class="sxs-lookup"><span data-stu-id="8d57d-290">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="8d57d-291">终结点和中间件与其他基于 ASP.NET Core 的技术（如运行状况检查）很好地结合。</span><span class="sxs-lookup"><span data-stu-id="8d57d-291">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="8d57d-292">终结点可以在中间件和 MVC 中实现策略，例如 CORS 或授权。</span><span class="sxs-lookup"><span data-stu-id="8d57d-292">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="8d57d-293">可以将筛选器和属性置于控制器的方法中。</span><span class="sxs-lookup"><span data-stu-id="8d57d-293">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="8d57d-294">有关详细信息，请参阅 <xref:fundamentals/routing#routing-basics>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-294">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="8d57d-295">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="8d57d-295">Health Checks</span></span>

<span data-ttu-id="8d57d-296">运行状况检查将终结点路由与泛型主机一起使用。</span><span class="sxs-lookup"><span data-stu-id="8d57d-296">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="8d57d-297">在 `Startup.Configure` 内，使用终结点 URL 或相对路径在终结点生成器上调用 `MapHealthChecks`：</span><span class="sxs-lookup"><span data-stu-id="8d57d-297">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="8d57d-298">运行状况检查终结点可以：</span><span class="sxs-lookup"><span data-stu-id="8d57d-298">Health Checks endpoints can:</span></span>

* <span data-ttu-id="8d57d-299">指定一个或多个允许的主机/端口。</span><span class="sxs-lookup"><span data-stu-id="8d57d-299">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="8d57d-300">需要授权。</span><span class="sxs-lookup"><span data-stu-id="8d57d-300">Require authorization.</span></span>
* <span data-ttu-id="8d57d-301">需要 CORS。</span><span class="sxs-lookup"><span data-stu-id="8d57d-301">Require CORS.</span></span>

<span data-ttu-id="8d57d-302">有关详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="8d57d-302">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="8d57d-303">HttpContext 上的管道</span><span class="sxs-lookup"><span data-stu-id="8d57d-303">Pipes on HttpContext</span></span>

<span data-ttu-id="8d57d-304">现在可以使用 <xref:System.IO.Pipelines> API 读取请求正文和写入响应正文。</span><span class="sxs-lookup"><span data-stu-id="8d57d-304">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="8d57d-305">必须向</span><span class="sxs-lookup"><span data-stu-id="8d57d-305">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="8d57d-306">`HttpRequest.BodyReader` 属性提供可用于读取请求正文的 <xref:System.IO.Pipelines.PipeReader>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-306">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="8d57d-307">必须向</span><span class="sxs-lookup"><span data-stu-id="8d57d-307">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="8d57d-308">`HttpResponse.BodyWriter` 属性提供可用于写入响应正文的 <xref:System.IO.Pipelines.PipeWriter>。</span><span class="sxs-lookup"><span data-stu-id="8d57d-308">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="8d57d-309">`HttpRequest.BodyReader` 是 `HttpRequest.Body` 流的模拟。</span><span class="sxs-lookup"><span data-stu-id="8d57d-309">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="8d57d-310">`HttpResponse.BodyWriter` 是 `HttpResponse.Body` 流的模拟。</span><span class="sxs-lookup"><span data-stu-id="8d57d-310">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="8d57d-311">改进了 IIS 中的错误报告</span><span class="sxs-lookup"><span data-stu-id="8d57d-311">Improved error reporting in IIS</span></span>

<span data-ttu-id="8d57d-312">在 IIS 中托管 ASP.NET Core 应用时出现的启动错误现在会生成更丰富的诊断数据。</span><span class="sxs-lookup"><span data-stu-id="8d57d-312">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="8d57d-313">如果适用，这些错误将报告给具有堆栈跟踪的 Windows 事件日志。</span><span class="sxs-lookup"><span data-stu-id="8d57d-313">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="8d57d-314">此外，所有警告、错误和未经处理的异常都将记录到 Windows 事件日志中。</span><span class="sxs-lookup"><span data-stu-id="8d57d-314">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="8d57d-315">辅助角色服务和辅助角色 SDK</span><span class="sxs-lookup"><span data-stu-id="8d57d-315">Worker Service and Worker SDK</span></span>

<span data-ttu-id="8d57d-316">.NET Core 3.0 引入了新的辅助角色服务应用模板。</span><span class="sxs-lookup"><span data-stu-id="8d57d-316">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="8d57d-317">可根据此模板开始在 .NET Core 中编写长期运行的服务。</span><span class="sxs-lookup"><span data-stu-id="8d57d-317">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="8d57d-318">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="8d57d-318">For more information, see:</span></span>

* [<span data-ttu-id="8d57d-319">作为 Windows 服务的 .NET Core 辅助角色</span><span class="sxs-lookup"><span data-stu-id="8d57d-319">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="8d57d-320">转接标头中间件改进</span><span class="sxs-lookup"><span data-stu-id="8d57d-320">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="8d57d-321">在以前版本的 ASP.NET Core 中，在部署到 Azure Linux 或除 IIS 以外的任何反向代理后，调用 <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> 和 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> 会出现问题。</span><span class="sxs-lookup"><span data-stu-id="8d57d-321">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="8d57d-322">[转发 Linux 和非 IIS 反向代理的方案](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)记录了以前版本的修复。</span><span class="sxs-lookup"><span data-stu-id="8d57d-322">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="8d57d-323">此方案已在 ASP.NET Core 3.0 中修复。</span><span class="sxs-lookup"><span data-stu-id="8d57d-323">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="8d57d-324">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 环境变量设置为 `true`，则主机启用[转接的标头中间件](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-324">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="8d57d-325">在容器映像中，`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="8d57d-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="8d57d-326">性能改进</span><span class="sxs-lookup"><span data-stu-id="8d57d-326">Performance improvements</span></span>

<span data-ttu-id="8d57d-327">ASP.NET Core 3.0 包含了许多改进，可减少内存使用量并提高吞吐量：</span><span class="sxs-lookup"><span data-stu-id="8d57d-327">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="8d57d-328">降低了使用内置的依赖项注入容器来实现作用域服务时的内存使用量。</span><span class="sxs-lookup"><span data-stu-id="8d57d-328">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="8d57d-329">减少跨框架的分配，包括中间件方案和路由。</span><span class="sxs-lookup"><span data-stu-id="8d57d-329">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="8d57d-330">降低了 WebSocket 连接的内存使用量。</span><span class="sxs-lookup"><span data-stu-id="8d57d-330">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="8d57d-331">减少 HTTPS 连接的内存使用量并提高了其吞吐量。</span><span class="sxs-lookup"><span data-stu-id="8d57d-331">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="8d57d-332">新的优化和完全异步 JSON 序列化程序。</span><span class="sxs-lookup"><span data-stu-id="8d57d-332">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="8d57d-333">减少了窗体分析的内存使用量并提高了其吞吐量。</span><span class="sxs-lookup"><span data-stu-id="8d57d-333">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="8d57d-334">ASP.NET Core 3.0 仅在 .NET Core 3.0 上运行</span><span class="sxs-lookup"><span data-stu-id="8d57d-334">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="8d57d-335">从 ASP.NET Core 3.0 开始，.NET Framework 不再是受支持的目标框架。</span><span class="sxs-lookup"><span data-stu-id="8d57d-335">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="8d57d-336">面向 .NET Framework 的项目可以使用 [.NET Core 2.1 LTS 版本](https://dotnet.microsoft.com/download/dotnet-core/2.1)以完全受支持的方式继续。</span><span class="sxs-lookup"><span data-stu-id="8d57d-336">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="8d57d-337">超过 .NET Core 2.1 的 3 年 LTS 期之后，将无限期地向大多数 ASP.NET Core 2.1.x 相关包提供支持。</span><span class="sxs-lookup"><span data-stu-id="8d57d-337">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="8d57d-338">有关迁移信息，请参阅[将代码从 .NET Framework 移植到 .NET Core](/dotnet/core/porting/)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-338">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="8d57d-339">使用 ASP.NET Core 共享框架</span><span class="sxs-lookup"><span data-stu-id="8d57d-339">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="8d57d-340">[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含的 ASP.NET Core 3.0 共享框架不再需要项目文件中有一个显式 `<PackageReference />` 元素。</span><span class="sxs-lookup"><span data-stu-id="8d57d-340">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="8d57d-341">使用项目文件中的 `Microsoft.NET.Sdk.Web` SDK 时，会自动引用共享框架：</span><span class="sxs-lookup"><span data-stu-id="8d57d-341">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="8d57d-342">从 ASP.NET Core 共享框架中删除的程序集</span><span class="sxs-lookup"><span data-stu-id="8d57d-342">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="8d57d-343">从 ASP.NET Core 3.0 共享框架中删除的最值得注意的程序集有：</span><span class="sxs-lookup"><span data-stu-id="8d57d-343">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="8d57d-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="8d57d-345">若要将 Json.NET 添加到 ASP.NET Core 3.0，请参阅[添加基于 Newtonsoft.Json 的 JSON 格式支持](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-345">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="8d57d-346">ASP.NET Core 3.0 引入了 `System.Text.Json` 以读取和写入 JSON。</span><span class="sxs-lookup"><span data-stu-id="8d57d-346">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="8d57d-347">有关详细信息，请参阅本文档中的[新 JSON 序列化](#new-json-serialization)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-347">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="8d57d-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8d57d-348">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="8d57d-349">有关从共享框架中删除的程序集的完整列表，请参阅[从 Microsoft.AspNetCore.App 3.0 中删除的程序集](https://github.com/dotnet/AspNetCore/issues/3755)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-349">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="8d57d-350">有关此更改的动机的详细信息，请参阅 [3.0 中对 Microsoft.AspNetCore.App 所做的重大变更](https://github.com/aspnet/Announcements/issues/325)和[首先查看 ASP.NET Core 3.0 中的变更](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/)。</span><span class="sxs-lookup"><span data-stu-id="8d57d-350">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 