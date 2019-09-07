---
title: ASP.NET Core Blazor 宿主模型
author: guardrex
description: 了解 Blazor 客户端和服务器端的托管模型。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/hosting-models
ms.openlocfilehash: f7a16d64e1f874a4f6b3c8db5217810b13c7c6ff
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800425"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="c3113-103">ASP.NET Core Blazor 宿主模型</span><span class="sxs-lookup"><span data-stu-id="c3113-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="c3113-104">作者： [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c3113-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c3113-105">Blazor 是一种 web 框架，旨在在基于[WebAssembly](https://webassembly.org/)的 .net 运行时（*Blazor 客户*端）上的浏览器中运行客户端，或在 ASP.NET Core （*Blazor 服务器端*）中使用服务器端。</span><span class="sxs-lookup"><span data-stu-id="c3113-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor client-side*) or server-side in ASP.NET Core (*Blazor server-side*).</span></span> <span data-ttu-id="c3113-106">无论采用何种托管模型，应用和组件模型*都是相同*的。</span><span class="sxs-lookup"><span data-stu-id="c3113-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="c3113-107">若要为本文中所述的宿主模型创建项目，请<xref:blazor/get-started>参阅。</span><span class="sxs-lookup"><span data-stu-id="c3113-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="client-side"></a><span data-ttu-id="c3113-108">客户端</span><span class="sxs-lookup"><span data-stu-id="c3113-108">Client-side</span></span>

<span data-ttu-id="c3113-109">Blazor 的主体托管模型在 WebAssembly 上的浏览器中运行客户端。</span><span class="sxs-lookup"><span data-stu-id="c3113-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="c3113-110">将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="c3113-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="c3113-111">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="c3113-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="c3113-112">UI 更新和事件处理发生在同一进程中。</span><span class="sxs-lookup"><span data-stu-id="c3113-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="c3113-113">应用的资产将作为静态文件部署到 web 服务器或可为客户端提供静态内容的服务。</span><span class="sxs-lookup"><span data-stu-id="c3113-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor 客户端：Blazor 应用程序在浏览器中的 UI 线程上运行。](hosting-models/_static/client-side.png)

<span data-ttu-id="c3113-115">若要使用客户端托管模型创建 Blazor 应用，请使用**Blazor WebAssembly 应用**模板（[dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)）。</span><span class="sxs-lookup"><span data-stu-id="c3113-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="c3113-116">选择**Blazor WebAssembly 应用程序**模板之后，你可以选择将应用配置为使用 ASP.NET Core 后端，方法是选择 " **ASP.NET Core 托管**" 复选框（"[dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)"）。</span><span class="sxs-lookup"><span data-stu-id="c3113-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="c3113-117">ASP.NET Core 应用程序将 Blazor 应用程序提供给客户端。</span><span class="sxs-lookup"><span data-stu-id="c3113-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="c3113-118">Blazor 客户端应用可通过网络使用 web API 调用或[SignalR](xref:signalr/introduction)与服务器交互。</span><span class="sxs-lookup"><span data-stu-id="c3113-118">The Blazor client-side app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="c3113-119">这些模板包含处理以下内容的*blazor. webassembly*脚本：</span><span class="sxs-lookup"><span data-stu-id="c3113-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="c3113-120">下载 .NET 运行时、应用程序和应用程序的依赖项。</span><span class="sxs-lookup"><span data-stu-id="c3113-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="c3113-121">用于运行应用程序的运行时初始化。</span><span class="sxs-lookup"><span data-stu-id="c3113-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="c3113-122">客户端托管模型具有以下几个优点：</span><span class="sxs-lookup"><span data-stu-id="c3113-122">The client-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="c3113-123">没有 .NET 服务器端依赖项。</span><span class="sxs-lookup"><span data-stu-id="c3113-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="c3113-124">应用在下载到客户端之后完全正常运行。</span><span class="sxs-lookup"><span data-stu-id="c3113-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="c3113-125">完全利用客户端资源和功能。</span><span class="sxs-lookup"><span data-stu-id="c3113-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="c3113-126">工作从服务器卸载到客户端。</span><span class="sxs-lookup"><span data-stu-id="c3113-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="c3113-127">不需要 ASP.NET Core web 服务器来托管应用程序。</span><span class="sxs-lookup"><span data-stu-id="c3113-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="c3113-128">无服务器部署方案可能（例如，通过 CDN 提供应用）。</span><span class="sxs-lookup"><span data-stu-id="c3113-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="c3113-129">客户端托管有缺点：</span><span class="sxs-lookup"><span data-stu-id="c3113-129">There are downsides to client-side hosting:</span></span>

* <span data-ttu-id="c3113-130">应用程序限制为浏览器的功能。</span><span class="sxs-lookup"><span data-stu-id="c3113-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="c3113-131">需要支持的客户端硬件和软件（例如，WebAssembly 支持）。</span><span class="sxs-lookup"><span data-stu-id="c3113-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="c3113-132">下载大小较大，应用需要较长时间才能加载。</span><span class="sxs-lookup"><span data-stu-id="c3113-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="c3113-133">.NET 运行时和工具支持不太成熟。</span><span class="sxs-lookup"><span data-stu-id="c3113-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="c3113-134">例如， [.NET Standard](/dotnet/standard/net-standard)支持和调试中存在限制。</span><span class="sxs-lookup"><span data-stu-id="c3113-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="server-side"></a><span data-ttu-id="c3113-135">服务器端</span><span class="sxs-lookup"><span data-stu-id="c3113-135">Server-side</span></span>

<span data-ttu-id="c3113-136">使用服务器端承载模型，可在服务器上从 ASP.NET Core 的应用程序中执行应用。</span><span class="sxs-lookup"><span data-stu-id="c3113-136">With the server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="c3113-137">UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。</span><span class="sxs-lookup"><span data-stu-id="c3113-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![浏览器通过 SignalR 连接与服务器上 ASP.NET Core 应用程序内托管的应用程序进行交互。](hosting-models/_static/server-side.png)

<span data-ttu-id="c3113-139">若要使用服务器端承载模型创建 Blazor 应用，请使用 ASP.NET Core **Blazor 服务器应用**模板（[dotnet new blazorserver](/dotnet/core/tools/dotnet-new)）。</span><span class="sxs-lookup"><span data-stu-id="c3113-139">To create a Blazor app using the server-side hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="c3113-140">ASP.NET Core 应用托管服务器端应用，并创建客户端连接到的 SignalR 终结点。</span><span class="sxs-lookup"><span data-stu-id="c3113-140">The ASP.NET Core app hosts the server-side app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="c3113-141">ASP.NET Core 应用引用要添加的应用`Startup`的类：</span><span class="sxs-lookup"><span data-stu-id="c3113-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="c3113-142">服务器端服务。</span><span class="sxs-lookup"><span data-stu-id="c3113-142">Server-side services.</span></span>
* <span data-ttu-id="c3113-143">请求处理管道的应用。</span><span class="sxs-lookup"><span data-stu-id="c3113-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="c3113-144">*Blazor*脚本&dagger;建立客户端连接。</span><span class="sxs-lookup"><span data-stu-id="c3113-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="c3113-145">应用负责根据需要保存和还原应用状态（例如，在网络连接丢失的情况下）。</span><span class="sxs-lookup"><span data-stu-id="c3113-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="c3113-146">服务器端托管模型具有以下几个优点：</span><span class="sxs-lookup"><span data-stu-id="c3113-146">The server-side hosting model offers several benefits:</span></span>

* <span data-ttu-id="c3113-147">下载大小明显小于客户端应用，且应用加载速度更快。</span><span class="sxs-lookup"><span data-stu-id="c3113-147">Download size is significantly smaller than a client-side app, and the app loads much faster.</span></span>
* <span data-ttu-id="c3113-148">应用充分利用服务器功能，包括使用任何与 .NET Core 兼容的 Api。</span><span class="sxs-lookup"><span data-stu-id="c3113-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="c3113-149">服务器上的 .NET Core 用于运行应用程序，因此现有的 .NET 工具（如调试）可按预期方式工作。</span><span class="sxs-lookup"><span data-stu-id="c3113-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="c3113-150">支持瘦客户端。</span><span class="sxs-lookup"><span data-stu-id="c3113-150">Thin clients are supported.</span></span> <span data-ttu-id="c3113-151">例如，服务器端应用程序适用于不支持 WebAssembly 的浏览器和资源限制的设备。</span><span class="sxs-lookup"><span data-stu-id="c3113-151">For example, server-side apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="c3113-152">应用程序的 .NET/C#代码库（包括应用程序的组件代码）不会提供给客户端。</span><span class="sxs-lookup"><span data-stu-id="c3113-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="c3113-153">服务器端托管有缺点：</span><span class="sxs-lookup"><span data-stu-id="c3113-153">There are downsides to server-side hosting:</span></span>

* <span data-ttu-id="c3113-154">通常存在较高的延迟。</span><span class="sxs-lookup"><span data-stu-id="c3113-154">Higher latency usually exists.</span></span> <span data-ttu-id="c3113-155">每个用户交互都涉及网络跃点。</span><span class="sxs-lookup"><span data-stu-id="c3113-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="c3113-156">无脱机支持。</span><span class="sxs-lookup"><span data-stu-id="c3113-156">There's no offline support.</span></span> <span data-ttu-id="c3113-157">如果客户端连接失败，应用将停止工作。</span><span class="sxs-lookup"><span data-stu-id="c3113-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="c3113-158">对于包含多个用户的应用而言，可伸缩性非常困难。</span><span class="sxs-lookup"><span data-stu-id="c3113-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="c3113-159">服务器必须管理多个客户端连接并处理客户端状态。</span><span class="sxs-lookup"><span data-stu-id="c3113-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="c3113-160">为应用提供服务需要 ASP.NET Core 服务器。</span><span class="sxs-lookup"><span data-stu-id="c3113-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="c3113-161">不可能的无服务器部署方案（例如，通过 CDN 为应用提供服务）。</span><span class="sxs-lookup"><span data-stu-id="c3113-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="c3113-162">&dagger;*Blazor*脚本是从 ASP.NET Core 共享框架中的嵌入资源提供的。</span><span class="sxs-lookup"><span data-stu-id="c3113-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="c3113-163">重新连接到同一台服务器</span><span class="sxs-lookup"><span data-stu-id="c3113-163">Reconnection to the same server</span></span>

<span data-ttu-id="c3113-164">Blazor 服务器端应用需要与服务器建立活动的 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="c3113-164">Blazor server-side apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="c3113-165">如果连接丢失，应用会尝试重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="c3113-165">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="c3113-166">只要客户端的状态仍在内存中，客户端会话便会恢复而不会失去状态。</span><span class="sxs-lookup"><span data-stu-id="c3113-166">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>
 
<span data-ttu-id="c3113-167">当客户端检测到连接已丢失时，用户会在客户端尝试重新连接时向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="c3113-167">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="c3113-168">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="c3113-168">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="c3113-169">若要自定义 UI，请在`components-reconnect-modal` *_Host* Razor 页`id`中使用作为其定义的元素。</span><span class="sxs-lookup"><span data-stu-id="c3113-169">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="c3113-170">客户端根据连接状态将此元素更新为下面的一个 CSS 类：</span><span class="sxs-lookup"><span data-stu-id="c3113-170">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>
 
* <span data-ttu-id="c3113-171">`components-reconnect-show`&ndash;显示 UI 以指示连接已丢失，并且客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="c3113-171">`components-reconnect-show` &ndash; Show the UI to indicate the connection was lost and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="c3113-172">`components-reconnect-hide`&ndash;客户端具有活动的连接，隐藏 UI。</span><span class="sxs-lookup"><span data-stu-id="c3113-172">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="c3113-173">`components-reconnect-failed`&ndash;重新连接失败。</span><span class="sxs-lookup"><span data-stu-id="c3113-173">`components-reconnect-failed` &ndash; Reconnection failed.</span></span> <span data-ttu-id="c3113-174">若要再次尝试重新连接`window.Blazor.reconnect()`，请调用。</span><span class="sxs-lookup"><span data-stu-id="c3113-174">To attempt reconnection again, call `window.Blazor.reconnect()`.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="c3113-175">预呈现后有状态重新连接</span><span class="sxs-lookup"><span data-stu-id="c3113-175">Stateful reconnection after prerendering</span></span>
 
<span data-ttu-id="c3113-176">默认情况下，Blazor 服务器端应用设置为在客户端与服务器建立连接之前，在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="c3113-176">Blazor server-side apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="c3113-177">这是在 *_Host* Razor 页面中设置的：</span><span class="sxs-lookup"><span data-stu-id="c3113-177">This is set up in the *_Host.cshtml* Razor page:</span></span>
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="c3113-178">`RenderMode`配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="c3113-178">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="c3113-179">已预呈现到页面中。</span><span class="sxs-lookup"><span data-stu-id="c3113-179">Is prerendered into the page.</span></span>
* <span data-ttu-id="c3113-180">在页面上呈现为静态 HTML，或者，如果包含从用户代理启动 Blazor 应用所需的信息，则为。</span><span class="sxs-lookup"><span data-stu-id="c3113-180">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="c3113-181">描述</span><span class="sxs-lookup"><span data-stu-id="c3113-181">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="c3113-182">将组件呈现为静态 HTML，并包含 Blazor 服务器端应用程序的标记。</span><span class="sxs-lookup"><span data-stu-id="c3113-182">Renders the component into static HTML and includes a marker for a Blazor server-side app.</span></span> <span data-ttu-id="c3113-183">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="c3113-183">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="c3113-184">不支持参数。</span><span class="sxs-lookup"><span data-stu-id="c3113-184">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="c3113-185">呈现 Blazor 服务器端应用程序的标记。</span><span class="sxs-lookup"><span data-stu-id="c3113-185">Renders a marker for a Blazor server-side app.</span></span> <span data-ttu-id="c3113-186">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="c3113-186">Output from the component isn't included.</span></span> <span data-ttu-id="c3113-187">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="c3113-187">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="c3113-188">不支持参数。</span><span class="sxs-lookup"><span data-stu-id="c3113-188">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="c3113-189">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="c3113-189">Renders the component into static HTML.</span></span> <span data-ttu-id="c3113-190">支持参数。</span><span class="sxs-lookup"><span data-stu-id="c3113-190">Parameters are supported.</span></span> |

<span data-ttu-id="c3113-191">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="c3113-191">Rendering server components from a static HTML page isn't supported.</span></span>
 
<span data-ttu-id="c3113-192">客户端重新连接到服务器，该服务器具有用于预呈现应用的相同状态。</span><span class="sxs-lookup"><span data-stu-id="c3113-192">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="c3113-193">如果应用的状态仍在内存中，则在建立 SignalR 连接后，组件状态将不重新呈现。</span><span class="sxs-lookup"><span data-stu-id="c3113-193">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="c3113-194">从 Razor 页面和视图呈现有状态交互式组件</span><span class="sxs-lookup"><span data-stu-id="c3113-194">Render stateful interactive components from Razor pages and views</span></span>
 
<span data-ttu-id="c3113-195">可以将有状态交互组件添加到 Razor 页面或视图。</span><span class="sxs-lookup"><span data-stu-id="c3113-195">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="c3113-196">呈现页面或视图时：</span><span class="sxs-lookup"><span data-stu-id="c3113-196">When the page or view renders:</span></span>

* <span data-ttu-id="c3113-197">该组件与页面或视图预呈现。</span><span class="sxs-lookup"><span data-stu-id="c3113-197">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="c3113-198">用于预呈现的初始组件状态将丢失。</span><span class="sxs-lookup"><span data-stu-id="c3113-198">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="c3113-199">建立 SignalR 连接时，将创建新的组件状态。</span><span class="sxs-lookup"><span data-stu-id="c3113-199">New component state is created when the SignalR connection is established.</span></span>
 
<span data-ttu-id="c3113-200">以下 Razor 页面呈现`Counter`组件：</span><span class="sxs-lookup"><span data-stu-id="c3113-200">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>
 
@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="c3113-201">从 Razor 页面和视图呈现非交互式组件</span><span class="sxs-lookup"><span data-stu-id="c3113-201">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="c3113-202">在以下 Razor 页面中， `MyComponent`组件以静态方式呈现，其初始值是使用窗体指定的：</span><span class="sxs-lookup"><span data-stu-id="c3113-202">In the following Razor page, the `MyComponent` component is statically rendered with an initial value that's specified using a form:</span></span>
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>
 
@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="c3113-203">由于`MyComponent`是静态呈现的，因此该组件不能是交互式的。</span><span class="sxs-lookup"><span data-stu-id="c3113-203">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="c3113-204">检测预呈现应用的时间</span><span class="sxs-lookup"><span data-stu-id="c3113-204">Detect when the app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a><span data-ttu-id="c3113-205">为 Blazor 服务器端应用配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="c3113-205">Configure the SignalR client for Blazor server-side apps</span></span>
 
<span data-ttu-id="c3113-206">有时，你需要配置 Blazor 服务器端应用使用的 SignalR 客户端。</span><span class="sxs-lookup"><span data-stu-id="c3113-206">Sometimes, you need to configure the SignalR client used by Blazor server-side apps.</span></span> <span data-ttu-id="c3113-207">例如，你可能想要在 SignalR 客户端上配置日志记录以诊断连接问题。</span><span class="sxs-lookup"><span data-stu-id="c3113-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>
 
<span data-ttu-id="c3113-208">若要在*Pages/_Host*文件中配置 SignalR 客户端：</span><span class="sxs-lookup"><span data-stu-id="c3113-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="c3113-209">将属性添加到 blazor `<script>`脚本的标记中。 `autostart="false"`</span><span class="sxs-lookup"><span data-stu-id="c3113-209">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="c3113-210">调用`Blazor.start`并传入指定 SignalR 生成器的配置对象。</span><span class="sxs-lookup"><span data-stu-id="c3113-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="c3113-211">其他资源</span><span class="sxs-lookup"><span data-stu-id="c3113-211">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
