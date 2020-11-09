---
title: 'ASP.NET Core Blazor 状态管理'
author: guardrex
description: '了解如何在 Blazor Server 应用中保留状态。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 1769ddbb95c9ffe373e916c885e411adc3d4c65b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054991"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="20658-103">ASP.NET Core Blazor 状态管理</span><span class="sxs-lookup"><span data-stu-id="20658-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="20658-104">作者：[Steve Sanderson](https://github.com/SteveSandersonMS) 及 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="20658-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="20658-105">在 Blazor WebAssembly 应用中创建的用户状态会保存在浏览器的内存中。</span><span class="sxs-lookup"><span data-stu-id="20658-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="20658-106">浏览器内存中保留的用户状态的示例：</span><span class="sxs-lookup"><span data-stu-id="20658-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="20658-107">呈现的 UI 中组件实例的层次结构及其最新的呈现输出。</span><span class="sxs-lookup"><span data-stu-id="20658-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="20658-108">组件实例中的字段和属性的值。</span><span class="sxs-lookup"><span data-stu-id="20658-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="20658-109">[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="20658-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="20658-110">通过 [JavaScript 互操作](xref:blazor/call-javascript-from-dotnet)调用设置的值。</span><span class="sxs-lookup"><span data-stu-id="20658-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="20658-111">当用户关闭并重新打开其浏览器或重新加载页面时，浏览器的内存中保存的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="20658-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="20658-112">跨浏览器会话保留状态</span><span class="sxs-lookup"><span data-stu-id="20658-112">Persist state across browser sessions</span></span>

<span data-ttu-id="20658-113">通常情况下，在用户主动创建数据，而不是简单地读取已存在的数据时，会跨浏览器会话保持状态。</span><span class="sxs-lookup"><span data-stu-id="20658-113">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="20658-114">若要跨浏览器会话保留状态，应用必须将数据保存到浏览器的内存以外的其他存储位置。</span><span class="sxs-lookup"><span data-stu-id="20658-114">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="20658-115">状态暂留并非是自动进行的。</span><span class="sxs-lookup"><span data-stu-id="20658-115">State persistence isn't automatic.</span></span> <span data-ttu-id="20658-116">必须在开发应用时采取措施来实现有状态的数据暂留。</span><span class="sxs-lookup"><span data-stu-id="20658-116">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="20658-117">通常，只有用户投入了大量精力所创建的高价值状态才需要数据暂留。</span><span class="sxs-lookup"><span data-stu-id="20658-117">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="20658-118">在下面的示例中，保留状态可以节省时间或有助于商业活动：</span><span class="sxs-lookup"><span data-stu-id="20658-118">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="20658-119">多步骤 Web 窗体：如果多步骤 Web 窗体的多个已完成步骤的状态丢失，用户重新输入这些步骤的数据会非常耗时。</span><span class="sxs-lookup"><span data-stu-id="20658-119">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="20658-120">如果用户离开窗体并在稍后返回，在这种应用场景下，用户将丢失状态。</span><span class="sxs-lookup"><span data-stu-id="20658-120">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="20658-121">购物车：应用中任何代表潜在收入且具有重要商业价值的组件都可以保留。</span><span class="sxs-lookup"><span data-stu-id="20658-121">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="20658-122">如果用户丢失了其状态，进而丢失了其购物车，则在他们稍后返回站点时可购买较少的产品或服务。</span><span class="sxs-lookup"><span data-stu-id="20658-122">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="20658-123">应用只能保留应用状态。</span><span class="sxs-lookup"><span data-stu-id="20658-123">An app can only persist *app state*.</span></span> <span data-ttu-id="20658-124">不能保留 UI，如组件实例及其呈现树。</span><span class="sxs-lookup"><span data-stu-id="20658-124">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="20658-125">组件和呈现树通常不能序列化。</span><span class="sxs-lookup"><span data-stu-id="20658-125">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="20658-126">若要保留 UI 状态（如树视图控件的展开节点），应用必须使用自定义代码将 UI 状态行为建模为可序列化应用状态。</span><span class="sxs-lookup"><span data-stu-id="20658-126">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="20658-127">保留状态的位置</span><span class="sxs-lookup"><span data-stu-id="20658-127">Where to persist state</span></span>

<span data-ttu-id="20658-128">用于保留状态的常见位置有：</span><span class="sxs-lookup"><span data-stu-id="20658-128">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="20658-129">服务器端存储</span><span class="sxs-lookup"><span data-stu-id="20658-129">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="20658-130">URL</span><span class="sxs-lookup"><span data-stu-id="20658-130">URL</span></span>](#url)
* [<span data-ttu-id="20658-131">浏览器存储</span><span class="sxs-lookup"><span data-stu-id="20658-131">Browser storage</span></span>](#browser-storage)
* [<span data-ttu-id="20658-132">内存中状态容器服务</span><span class="sxs-lookup"><span data-stu-id="20658-132">In-memory state container service</span></span>](#in-memory-state-container-service)

### <a name="server-side-storage"></a><span data-ttu-id="20658-133">服务器端存储</span><span class="sxs-lookup"><span data-stu-id="20658-133">Server-side storage</span></span>

<span data-ttu-id="20658-134">对于跨多个用户和设备的永久数据持久性，应用可以使用通过 Web API 访问的独立服务器端存储。</span><span class="sxs-lookup"><span data-stu-id="20658-134">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="20658-135">选项包括：</span><span class="sxs-lookup"><span data-stu-id="20658-135">Options include:</span></span>

* <span data-ttu-id="20658-136">Blob 存储</span><span class="sxs-lookup"><span data-stu-id="20658-136">Blob storage</span></span>
* <span data-ttu-id="20658-137">键值存储</span><span class="sxs-lookup"><span data-stu-id="20658-137">Key-value storage</span></span>
* <span data-ttu-id="20658-138">关系数据库</span><span class="sxs-lookup"><span data-stu-id="20658-138">Relational database</span></span>
* <span data-ttu-id="20658-139">表存储</span><span class="sxs-lookup"><span data-stu-id="20658-139">Table storage</span></span>

<span data-ttu-id="20658-140">保存数据后，将保留用户的状态，并在任何新的浏览器会话中可用。</span><span class="sxs-lookup"><span data-stu-id="20658-140">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="20658-141">由于 Blazor WebAssembly 应用完全在用户的浏览器中运行，因此它们需要额外的措施来访问安全的外部系统，如存储服务和数据库。</span><span class="sxs-lookup"><span data-stu-id="20658-141">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="20658-142">Blazor WebAssembly 应用的保护方式与单页应用 (SPA) 相同。</span><span class="sxs-lookup"><span data-stu-id="20658-142">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="20658-143">通常，应用通过 [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) 对用户进行身份验证，然后通过对服务器端应用的 Web API 调用与存储服务和数据库进行交互。</span><span class="sxs-lookup"><span data-stu-id="20658-143">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="20658-144">服务器端应用可协调 Blazor WebAssembly 应用与存储服务或数据库之间的数据传输。</span><span class="sxs-lookup"><span data-stu-id="20658-144">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="20658-145">Blazor WebAssembly 应用保持与服务器端应用的临时连接，而服务器端应用具有到存储的持久连接。</span><span class="sxs-lookup"><span data-stu-id="20658-145">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="20658-146">有关详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="20658-146">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="20658-147">Blazor 安全和 Identity 文章</span><span class="sxs-lookup"><span data-stu-id="20658-147">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="20658-148">有关 Azure 数据存储选项的详细信息，请参阅以下内容：</span><span class="sxs-lookup"><span data-stu-id="20658-148">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="20658-149">Azure 数据库</span><span class="sxs-lookup"><span data-stu-id="20658-149">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="20658-150">Azure 存储文档</span><span class="sxs-lookup"><span data-stu-id="20658-150">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="20658-151">URL</span><span class="sxs-lookup"><span data-stu-id="20658-151">URL</span></span>

<span data-ttu-id="20658-152">对于表示导航状态的暂时性数据，请将数据作为 URL 的一部分进行建模。</span><span class="sxs-lookup"><span data-stu-id="20658-152">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="20658-153">URL 中建模的用户状态示例：</span><span class="sxs-lookup"><span data-stu-id="20658-153">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="20658-154">已查看实体的 ID。</span><span class="sxs-lookup"><span data-stu-id="20658-154">The ID of a viewed entity.</span></span>
* <span data-ttu-id="20658-155">分页网格中的当前页码。</span><span class="sxs-lookup"><span data-stu-id="20658-155">The current page number in a paged grid.</span></span>

<span data-ttu-id="20658-156">在用户手动重新加载页面时保留的浏览器地址栏的内容。</span><span class="sxs-lookup"><span data-stu-id="20658-156">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="20658-157">有关使用 [`@page`](xref:mvc/views/razor#page) 指令定义 URL 模式的信息，请参阅 <xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="20658-157">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="20658-158">浏览器存储</span><span class="sxs-lookup"><span data-stu-id="20658-158">Browser storage</span></span>

<span data-ttu-id="20658-159">对于用户正在主动创建的暂时性数据，通用存储位置是浏览器的 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 集合：</span><span class="sxs-lookup"><span data-stu-id="20658-159">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="20658-160">`localStorage` 的应用范围限定为浏览器的窗口。</span><span class="sxs-lookup"><span data-stu-id="20658-160">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="20658-161">如果用户重载页面或关闭并重新打开浏览器，则状态保持不变。</span><span class="sxs-lookup"><span data-stu-id="20658-161">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="20658-162">如果用户打开多个浏览器选项卡，则状态跨选项卡共享。</span><span class="sxs-lookup"><span data-stu-id="20658-162">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="20658-163">数据保留在 `localStorage` 中，直到被显式清除为止。</span><span class="sxs-lookup"><span data-stu-id="20658-163">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="20658-164">`sessionStorage` 的应用范围限定为浏览器的选项卡。如果用户重载该选项卡，则状态保持不变。</span><span class="sxs-lookup"><span data-stu-id="20658-164">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="20658-165">如果用户关闭该选项卡或该浏览器，则状态丢失。</span><span class="sxs-lookup"><span data-stu-id="20658-165">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="20658-166">如果用户打开多个浏览器选项卡，则每个选项卡都有自己独立的数据版本。</span><span class="sxs-lookup"><span data-stu-id="20658-166">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="20658-167">`localStorage` 和 `sessionStorage` 可用于 Blazor WebAssembly 应用，但只能通过编写自定义代码或使用第三方包的方式使用。</span><span class="sxs-lookup"><span data-stu-id="20658-167">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="20658-168">通常，`sessionStorage` 使用起来更安全。</span><span class="sxs-lookup"><span data-stu-id="20658-168">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="20658-169">`sessionStorage` 避免了用户打开多个选项卡并遇到以下问题的风险：</span><span class="sxs-lookup"><span data-stu-id="20658-169">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="20658-170">跨选项卡的状态存储中出现 bug。</span><span class="sxs-lookup"><span data-stu-id="20658-170">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="20658-171">一个选项卡覆盖其他选项卡的状态时出现混乱行为。</span><span class="sxs-lookup"><span data-stu-id="20658-171">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="20658-172">如果应用必须在关闭和重新打开浏览器期间保持状态，则 `localStorage` 是更好的选择。</span><span class="sxs-lookup"><span data-stu-id="20658-172">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="20658-173">用户可以查看或篡改 `localStorage` 和 `sessionStorage` 中存储的数据。</span><span class="sxs-lookup"><span data-stu-id="20658-173">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

## <a name="in-memory-state-container-service"></a><span data-ttu-id="20658-174">内存中状态容器服务</span><span class="sxs-lookup"><span data-stu-id="20658-174">In-memory state container service</span></span>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="20658-175">其他资源</span><span class="sxs-lookup"><span data-stu-id="20658-175">Additional resources</span></span>

* [<span data-ttu-id="20658-176">在身份验证操作之前保存应用状态</span><span class="sxs-lookup"><span data-stu-id="20658-176">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="20658-177">Blazor Server 是有状态的应用框架。</span><span class="sxs-lookup"><span data-stu-id="20658-177">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="20658-178">大多数情况下，应用保持与服务器的连接。</span><span class="sxs-lookup"><span data-stu-id="20658-178">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="20658-179">用户的状态保留在线路中的服务器内存中。</span><span class="sxs-lookup"><span data-stu-id="20658-179">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="20658-180">线路中保留的用户状态示例：</span><span class="sxs-lookup"><span data-stu-id="20658-180">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="20658-181">呈现的 UI 中组件实例的层次结构及其最新的呈现输出。</span><span class="sxs-lookup"><span data-stu-id="20658-181">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="20658-182">组件实例中的字段和属性的值。</span><span class="sxs-lookup"><span data-stu-id="20658-182">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="20658-183">在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。</span><span class="sxs-lookup"><span data-stu-id="20658-183">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="20658-184">还可以通过 [JavaScript 互操作](xref:blazor/call-javascript-from-dotnet) 调用在浏览器的内存集的 JavaScript 变量中找到用户状态。</span><span class="sxs-lookup"><span data-stu-id="20658-184">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="20658-185">如果用户遇到暂时的网络连接丢失问题，Blazor 会尝试将用户重新连接到具有其原始状态的原始线路。</span><span class="sxs-lookup"><span data-stu-id="20658-185">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="20658-186">但是，将用户重新连接到服务器内存中的原始电路并非总是能够实现的：</span><span class="sxs-lookup"><span data-stu-id="20658-186">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="20658-187">服务器不能永久保留断开连接的线路。</span><span class="sxs-lookup"><span data-stu-id="20658-187">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="20658-188">超时后或在服务器面临内存压力时，服务器必须释放断开连接的线路。</span><span class="sxs-lookup"><span data-stu-id="20658-188">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="20658-189">在负载均衡的多服务器部署环境中，不再需要单个服务器处理整个请求量时，它可能会失败或被自动删除。</span><span class="sxs-lookup"><span data-stu-id="20658-189">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="20658-190">在用户尝试重新连接时，用户的原始服务器处理请求可能会变得不可用。</span><span class="sxs-lookup"><span data-stu-id="20658-190">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="20658-191">用户可能会关闭并重新打开其浏览器或重载页面，这会删除浏览器内存中保留的所有状态。</span><span class="sxs-lookup"><span data-stu-id="20658-191">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="20658-192">例如，通过 JavaScript 互操作调用设置的 JavaScript 变量值会丢失。</span><span class="sxs-lookup"><span data-stu-id="20658-192">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="20658-193">当无法将用户重新连接到其原始线路时，用户将收到一个具有空状态的新线路。</span><span class="sxs-lookup"><span data-stu-id="20658-193">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="20658-194">这等效于关闭并重新打开桌面应用。</span><span class="sxs-lookup"><span data-stu-id="20658-194">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="20658-195">跨线路保留状态</span><span class="sxs-lookup"><span data-stu-id="20658-195">Persist state across circuits</span></span>

<span data-ttu-id="20658-196">通常情况下，在用户主动创建数据，而不是简单地读取已存在的数据时，会跨线路保持状态。</span><span class="sxs-lookup"><span data-stu-id="20658-196">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="20658-197">若要跨线路保留状态，应用必须将数据保存到服务器的内存以外的其他存储位置。</span><span class="sxs-lookup"><span data-stu-id="20658-197">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="20658-198">状态暂留并非是自动进行的。</span><span class="sxs-lookup"><span data-stu-id="20658-198">State persistence isn't automatic.</span></span> <span data-ttu-id="20658-199">必须在开发应用时采取措施来实现有状态的数据暂留。</span><span class="sxs-lookup"><span data-stu-id="20658-199">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="20658-200">通常，只有用户投入了大量精力所创建的高价值状态才需要数据暂留。</span><span class="sxs-lookup"><span data-stu-id="20658-200">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="20658-201">在下面的示例中，保留状态可以节省时间或有助于商业活动：</span><span class="sxs-lookup"><span data-stu-id="20658-201">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="20658-202">多步骤 Web 窗体：如果多步骤 Web 窗体的多个已完成步骤的状态丢失，用户重新输入这些步骤的数据会非常耗时。</span><span class="sxs-lookup"><span data-stu-id="20658-202">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="20658-203">如果用户离开窗体并在稍后返回，在这种应用场景下，用户将丢失状态。</span><span class="sxs-lookup"><span data-stu-id="20658-203">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="20658-204">购物车：应用中任何代表潜在收入且具有重要商业价值的组件都可以保留。</span><span class="sxs-lookup"><span data-stu-id="20658-204">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="20658-205">如果用户丢失了其状态，进而丢失了其购物车，则在他们稍后返回站点时可购买较少的产品或服务。</span><span class="sxs-lookup"><span data-stu-id="20658-205">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="20658-206">应用只能保留应用状态。</span><span class="sxs-lookup"><span data-stu-id="20658-206">An app can only persist *app state*.</span></span> <span data-ttu-id="20658-207">不能保留 UI，如组件实例及其呈现树。</span><span class="sxs-lookup"><span data-stu-id="20658-207">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="20658-208">组件和呈现树通常不能序列化。</span><span class="sxs-lookup"><span data-stu-id="20658-208">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="20658-209">若要保留 UI 状态（如树视图控件的展开节点），应用必须使用自定义代码将 UI 状态行为建模为可序列化应用状态。</span><span class="sxs-lookup"><span data-stu-id="20658-209">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="20658-210">保留状态的位置</span><span class="sxs-lookup"><span data-stu-id="20658-210">Where to persist state</span></span>

<span data-ttu-id="20658-211">用于保留状态的常见位置有：</span><span class="sxs-lookup"><span data-stu-id="20658-211">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="20658-212">服务器端存储</span><span class="sxs-lookup"><span data-stu-id="20658-212">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="20658-213">URL</span><span class="sxs-lookup"><span data-stu-id="20658-213">URL</span></span>](#url)
* [<span data-ttu-id="20658-214">浏览器存储</span><span class="sxs-lookup"><span data-stu-id="20658-214">Browser storage</span></span>](#browser-storage)
* [<span data-ttu-id="20658-215">内存中状态容器服务</span><span class="sxs-lookup"><span data-stu-id="20658-215">In-memory state container service</span></span>](#in-memory-state-container-service)

### <a name="server-side-storage"></a><span data-ttu-id="20658-216">服务器端存储</span><span class="sxs-lookup"><span data-stu-id="20658-216">Server-side storage</span></span>

<span data-ttu-id="20658-217">对于跨多个用户和设备的永久数据持久性，应用可以使用服务器端存储。</span><span class="sxs-lookup"><span data-stu-id="20658-217">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="20658-218">选项包括：</span><span class="sxs-lookup"><span data-stu-id="20658-218">Options include:</span></span>

* <span data-ttu-id="20658-219">Blob 存储</span><span class="sxs-lookup"><span data-stu-id="20658-219">Blob storage</span></span>
* <span data-ttu-id="20658-220">键值存储</span><span class="sxs-lookup"><span data-stu-id="20658-220">Key-value storage</span></span>
* <span data-ttu-id="20658-221">关系数据库</span><span class="sxs-lookup"><span data-stu-id="20658-221">Relational database</span></span>
* <span data-ttu-id="20658-222">表存储</span><span class="sxs-lookup"><span data-stu-id="20658-222">Table storage</span></span>

<span data-ttu-id="20658-223">保存数据后，将保留用户的状态，并在任何新的线路中可用。</span><span class="sxs-lookup"><span data-stu-id="20658-223">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="20658-224">有关 Azure 数据存储选项的详细信息，请参阅以下内容：</span><span class="sxs-lookup"><span data-stu-id="20658-224">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="20658-225">Azure 数据库</span><span class="sxs-lookup"><span data-stu-id="20658-225">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="20658-226">Azure 存储文档</span><span class="sxs-lookup"><span data-stu-id="20658-226">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="20658-227">URL</span><span class="sxs-lookup"><span data-stu-id="20658-227">URL</span></span>

<span data-ttu-id="20658-228">对于表示导航状态的暂时性数据，请将数据作为 URL 的一部分进行建模。</span><span class="sxs-lookup"><span data-stu-id="20658-228">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="20658-229">URL 中建模的用户状态示例：</span><span class="sxs-lookup"><span data-stu-id="20658-229">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="20658-230">已查看实体的 ID。</span><span class="sxs-lookup"><span data-stu-id="20658-230">The ID of a viewed entity.</span></span>
* <span data-ttu-id="20658-231">分页网格中的当前页码。</span><span class="sxs-lookup"><span data-stu-id="20658-231">The current page number in a paged grid.</span></span>

<span data-ttu-id="20658-232">保留浏览器地址栏的内容：</span><span class="sxs-lookup"><span data-stu-id="20658-232">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="20658-233">如果用户手动重载页面。</span><span class="sxs-lookup"><span data-stu-id="20658-233">If the user manually reloads the page.</span></span>
* <span data-ttu-id="20658-234">如果 Web 服务器不可用，且用户被强制重载页面，以便连接到其他服务器。</span><span class="sxs-lookup"><span data-stu-id="20658-234">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="20658-235">有关使用 [`@page`](xref:mvc/views/razor#page) 指令定义 URL 模式的信息，请参阅 <xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="20658-235">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="20658-236">浏览器存储</span><span class="sxs-lookup"><span data-stu-id="20658-236">Browser storage</span></span>

<span data-ttu-id="20658-237">对于用户正在主动创建的暂时性数据，通用存储位置是浏览器的 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) 集合：</span><span class="sxs-lookup"><span data-stu-id="20658-237">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="20658-238">`localStorage` 的应用范围限定为浏览器的窗口。</span><span class="sxs-lookup"><span data-stu-id="20658-238">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="20658-239">如果用户重载页面或关闭并重新打开浏览器，则状态保持不变。</span><span class="sxs-lookup"><span data-stu-id="20658-239">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="20658-240">如果用户打开多个浏览器选项卡，则状态跨选项卡共享。</span><span class="sxs-lookup"><span data-stu-id="20658-240">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="20658-241">数据保留在 `localStorage` 中，直到被显式清除为止。</span><span class="sxs-lookup"><span data-stu-id="20658-241">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="20658-242">`sessionStorage` 的应用范围限定为浏览器的选项卡。如果用户重载该选项卡，则状态保持不变。</span><span class="sxs-lookup"><span data-stu-id="20658-242">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="20658-243">如果用户关闭该选项卡或该浏览器，则状态丢失。</span><span class="sxs-lookup"><span data-stu-id="20658-243">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="20658-244">如果用户打开多个浏览器选项卡，则每个选项卡都有自己独立的数据版本。</span><span class="sxs-lookup"><span data-stu-id="20658-244">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="20658-245">通常，`sessionStorage` 使用起来更安全。</span><span class="sxs-lookup"><span data-stu-id="20658-245">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="20658-246">`sessionStorage` 避免了用户打开多个选项卡并遇到以下问题的风险：</span><span class="sxs-lookup"><span data-stu-id="20658-246">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="20658-247">跨选项卡的状态存储中出现 bug。</span><span class="sxs-lookup"><span data-stu-id="20658-247">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="20658-248">一个选项卡覆盖其他选项卡的状态时出现混乱行为。</span><span class="sxs-lookup"><span data-stu-id="20658-248">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="20658-249">如果应用必须在关闭和重新打开浏览器期间保持状态，则 `localStorage` 是更好的选择。</span><span class="sxs-lookup"><span data-stu-id="20658-249">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="20658-250">使用浏览器存储时的注意事项：</span><span class="sxs-lookup"><span data-stu-id="20658-250">Caveats for using browser storage:</span></span>

* <span data-ttu-id="20658-251">与使用服务器端数据库类似，加载和保存数据都是异步的。</span><span class="sxs-lookup"><span data-stu-id="20658-251">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="20658-252">与服务器端数据库不同，在预呈现期间，存储不可用，因为在预呈现阶段，请求的页面在浏览器中不存在。</span><span class="sxs-lookup"><span data-stu-id="20658-252">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="20658-253">保留状态的位置对于 Blazor Server 应用，持久存储几千字节的数据是合理的。</span><span class="sxs-lookup"><span data-stu-id="20658-253">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="20658-254">超出几千字节后，你就须考虑性能影响，因为数据是跨网络加载和保存的。</span><span class="sxs-lookup"><span data-stu-id="20658-254">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="20658-255">用户可以查看或篡改数据。</span><span class="sxs-lookup"><span data-stu-id="20658-255">Users may view or tamper with the data.</span></span> <span data-ttu-id="20658-256">[ASP.NET Core 数据保护](xref:security/data-protection/introduction)可以降低风险。</span><span class="sxs-lookup"><span data-stu-id="20658-256">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="20658-257">例如，[ASP.NET Core 受保护的浏览器存储](#aspnet-core-protected-browser-storage)使用 ASP.NET Core 数据保护。</span><span class="sxs-lookup"><span data-stu-id="20658-257">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="20658-258">第三方 NuGet 包提供使用 `localStorage` 和 `sessionStorage` 时采用的 API。</span><span class="sxs-lookup"><span data-stu-id="20658-258">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="20658-259">值得考虑的是，选择一个透明地使用 [ASP.NET Core 数据保护](xref:security/data-protection/introduction)的包。</span><span class="sxs-lookup"><span data-stu-id="20658-259">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="20658-260">数据保护可对存储的数据进行加密，并降低篡改存储数据的潜在风险。</span><span class="sxs-lookup"><span data-stu-id="20658-260">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="20658-261">如果 JSON 序列化的数据以纯文本形式存储，则用户可以使用浏览器开发人员工具查看数据，还可以修改存储的数据。</span><span class="sxs-lookup"><span data-stu-id="20658-261">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="20658-262">保护数据并非总是一个问题，因为有些数据本质上可能是无足轻重的。</span><span class="sxs-lookup"><span data-stu-id="20658-262">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="20658-263">例如，读取或修改 UI 元素的存储颜色不会对用户或组织造成严重的安全风险。</span><span class="sxs-lookup"><span data-stu-id="20658-263">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="20658-264">避免允许用户检查或篡改敏感数据。</span><span class="sxs-lookup"><span data-stu-id="20658-264">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="20658-265">ASP.NET Core 受保护的浏览器存储</span><span class="sxs-lookup"><span data-stu-id="20658-265">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="20658-266">ASP.NET Core 受保护的浏览器存储将 [ASP.NET Core 数据保护](xref:security/data-protection/introduction)用于 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage)。</span><span class="sxs-lookup"><span data-stu-id="20658-266">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="20658-267">受保护的浏览器存储依赖于 ASP.NET Core 数据保护，仅支持用于 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="20658-267">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="20658-268">保存和加载组件中的数据</span><span class="sxs-lookup"><span data-stu-id="20658-268">Save and load data within a component</span></span>

<span data-ttu-id="20658-269">在需要将数据加载或保存到浏览器存储的任何组件中，使用 [`@inject`](xref:mvc/views/razor#inject) 指令注入以下任意一项的实例：</span><span class="sxs-lookup"><span data-stu-id="20658-269">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="20658-270">具体选择取决于要使用的浏览器存储位置。</span><span class="sxs-lookup"><span data-stu-id="20658-270">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="20658-271">在以下示例中，使用 `sessionStorage`：</span><span class="sxs-lookup"><span data-stu-id="20658-271">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="20658-272">可将 `@using` 指令放在应用的 `_Imports.razor` 文件而不是组件中。</span><span class="sxs-lookup"><span data-stu-id="20658-272">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="20658-273">使用 `_Imports.razor` 文件可使命名空间可用于应用的较大部分或整个应用。</span><span class="sxs-lookup"><span data-stu-id="20658-273">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="20658-274">若要在基于 Blazor Server 项目模板的应用的 `Counter` 组件中保留 `currentCount` 值，请修改 `IncrementCount` 方法以使用 `ProtectedSessionStore.SetAsync`：</span><span class="sxs-lookup"><span data-stu-id="20658-274">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="20658-275">在更大、更真实的应用中，存储单个字段是不太可能出现的情况。</span><span class="sxs-lookup"><span data-stu-id="20658-275">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="20658-276">应用更有可能存储包含复杂状态的整个模型对象。</span><span class="sxs-lookup"><span data-stu-id="20658-276">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="20658-277">`ProtectedSessionStore` 自动串行化和反序列化 JSON 数据以存储复杂的状态对象。</span><span class="sxs-lookup"><span data-stu-id="20658-277">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="20658-278">在前面的代码示例中，`currentCount` 数据存储为用户浏览器中的 `sessionStorage['count']`。</span><span class="sxs-lookup"><span data-stu-id="20658-278">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="20658-279">数据不会以纯文本形式存储，而是使用 ASP.NET Core 的数据保护进行保护。</span><span class="sxs-lookup"><span data-stu-id="20658-279">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="20658-280">如果在浏览器的开发人员控制台中评估了 `sessionStorage['count']`，则可以检查已加密的数据。</span><span class="sxs-lookup"><span data-stu-id="20658-280">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="20658-281">若要在用户稍后返回到 `Counter` 组件时（包括用于位于新线路上时）恢复 `currentCount` 数据，请使用 `ProtectedSessionStore.GetAsync`：</span><span class="sxs-lookup"><span data-stu-id="20658-281">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="20658-282">如果组件的参数包括导航状态，请调用 `ProtectedSessionStore.GetAsync` 并将非 `null` 结果分配给 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="20658-282">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="20658-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 仅在首次实例化组件时调用一次。</span><span class="sxs-lookup"><span data-stu-id="20658-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="20658-284">如果用户导航到不同的 URL，而仍然停留在相同的页面上，则 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 之后不会再次调用。</span><span class="sxs-lookup"><span data-stu-id="20658-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="20658-285">有关详细信息，请参阅 <xref:blazor/components/lifecycle>。</span><span class="sxs-lookup"><span data-stu-id="20658-285">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="20658-286">本节中的示例仅在服务器未启用预呈现的情况下有效。</span><span class="sxs-lookup"><span data-stu-id="20658-286">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="20658-287">启用预呈现后，会生成错误，说明由于正在预呈现组件，无法发起 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="20658-287">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="20658-288">禁用预呈现或添加其他代码以处理预呈现。</span><span class="sxs-lookup"><span data-stu-id="20658-288">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="20658-289">若要了解有关编写可处理预呈现的代码的详细信息，请参阅[处理预呈现](#handle-prerendering)一节。</span><span class="sxs-lookup"><span data-stu-id="20658-289">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="20658-290">处理加载状态</span><span class="sxs-lookup"><span data-stu-id="20658-290">Handle the loading state</span></span>

<span data-ttu-id="20658-291">由于浏览器存储是异步访问（通过网络连接进行访问）的，因此往往需要一段时间才能加载完数据并可供组件使用。</span><span class="sxs-lookup"><span data-stu-id="20658-291">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="20658-292">为获得最佳结果，请在加载进行过程中呈现加载状态消息，而不要显示空数据或默认数据。</span><span class="sxs-lookup"><span data-stu-id="20658-292">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="20658-293">一种方法是跟踪数据是否为 `null`（表示数据仍在加载）。</span><span class="sxs-lookup"><span data-stu-id="20658-293">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="20658-294">在默认 `Counter` 组件中，计数保留在 `int` 中。</span><span class="sxs-lookup"><span data-stu-id="20658-294">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="20658-295">通过将问号 (`?`) 添加到类型 (`int`)，[使 `currentCount` 可以为 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)：</span><span class="sxs-lookup"><span data-stu-id="20658-295">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="20658-296">请勿无条件地显示计数和“`Increment`”按钮，而是禁止通过检查 <xref:System.Nullable%601.HasValue%2A> 以加载完数据后才显示这些元素：</span><span class="sxs-lookup"><span data-stu-id="20658-296">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="20658-297">处理预呈现</span><span class="sxs-lookup"><span data-stu-id="20658-297">Handle prerendering</span></span>

<span data-ttu-id="20658-298">预呈现期间：</span><span class="sxs-lookup"><span data-stu-id="20658-298">During prerendering:</span></span>

* <span data-ttu-id="20658-299">与用户浏览器的交互式连接不存在。</span><span class="sxs-lookup"><span data-stu-id="20658-299">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="20658-300">浏览器尚无可在其中运行 JavaScript 代码的页面。</span><span class="sxs-lookup"><span data-stu-id="20658-300">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="20658-301">在预呈现期间，`localStorage` 或 `sessionStorage` 不可用。</span><span class="sxs-lookup"><span data-stu-id="20658-301">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="20658-302">如果组件尝试与存储进行交互，则会生成错误，说明由于正在预呈现组件，无法发起 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="20658-302">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="20658-303">解决此错误的一种方法是禁用预呈现。</span><span class="sxs-lookup"><span data-stu-id="20658-303">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="20658-304">如果应用大量使用基于浏览器的存储，则这通常是最佳选择。</span><span class="sxs-lookup"><span data-stu-id="20658-304">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="20658-305">预呈现会增加复杂性，且不会给应用带来好处，因为在 `localStorage` 或 `sessionStorage` 可用之前，应用无法预呈现任何有用的内容。</span><span class="sxs-lookup"><span data-stu-id="20658-305">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="20658-306">若要禁用预呈现，请打开 `Pages/_Host.cshtml` 文件，并将[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)的 `render-mode` 属性更改为 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>：</span><span class="sxs-lookup"><span data-stu-id="20658-306">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="20658-307">对于不使用 `localStorage` 或 `sessionStorage` 的其他页面，预呈现可能很有用。</span><span class="sxs-lookup"><span data-stu-id="20658-307">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="20658-308">若要保持预呈现状态，可延迟加载操作，直到浏览器连接到线路。</span><span class="sxs-lookup"><span data-stu-id="20658-308">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="20658-309">以下是存储计数器值的示例：</span><span class="sxs-lookup"><span data-stu-id="20658-309">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="20658-310">将状态保留提取到常见位置</span><span class="sxs-lookup"><span data-stu-id="20658-310">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="20658-311">如果多个组件依赖于基于浏览器的存储，则多次重新实现状态提供程序代码会造成代码重复。</span><span class="sxs-lookup"><span data-stu-id="20658-311">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="20658-312">若要避免代码重复，一种方法是创建一个封装了状态提供程序逻辑的状态提供程序父组件。</span><span class="sxs-lookup"><span data-stu-id="20658-312">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="20658-313">子组件可处理保留的数据，而无需考虑状态暂留机制。</span><span class="sxs-lookup"><span data-stu-id="20658-313">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="20658-314">在 `CounterStateProvider` 组件的以下示例中，将计数器数据保留到 `sessionStorage`：</span><span class="sxs-lookup"><span data-stu-id="20658-314">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="20658-315">`CounterStateProvider` 组件处理加载阶段的方式是在加载完成后才呈现其子内容。</span><span class="sxs-lookup"><span data-stu-id="20658-315">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="20658-316">若要使用 `CounterStateProvider` 组件，请围绕需要访问计数器状态的任何其他组件包装该组件的实例。</span><span class="sxs-lookup"><span data-stu-id="20658-316">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="20658-317">若要使某个应用中的所有组件都可以访问该状态，请围绕 `App` 组件 (`App.razor`) 中的 <xref:Microsoft.AspNetCore.Components.Routing.Router> 来包装 `CounterStateProvider` 组件：</span><span class="sxs-lookup"><span data-stu-id="20658-317">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="20658-318">已包装的组件接收并可修改保留的计数器状态。</span><span class="sxs-lookup"><span data-stu-id="20658-318">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="20658-319">以下 `Counter` 组件实现了该模式：</span><span class="sxs-lookup"><span data-stu-id="20658-319">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="20658-320">与 `ProtectedBrowserStorage` 进行交互无需前面的组件，该组件也不会处理“正在加载”阶段。</span><span class="sxs-lookup"><span data-stu-id="20658-320">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="20658-321">如前所述，若要处理预呈现，可对 `CounterStateProvider` 进行修改，以便所有使用计数器数据的组件均可自动处理预呈现。</span><span class="sxs-lookup"><span data-stu-id="20658-321">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="20658-322">有关详细信息，请参阅[处理预呈现](#handle-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="20658-322">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="20658-323">通常，建议在以下情况下使用状态提供程序父组件模式：</span><span class="sxs-lookup"><span data-stu-id="20658-323">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="20658-324">跨多个组件使用状态。</span><span class="sxs-lookup"><span data-stu-id="20658-324">To consume state across many components.</span></span>
* <span data-ttu-id="20658-325">只有一个顶级状态对象要保留时。</span><span class="sxs-lookup"><span data-stu-id="20658-325">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="20658-326">若要保留多个不同的状态对象并在不同位置使用不同的对象子集，最好避免全局保留状态。</span><span class="sxs-lookup"><span data-stu-id="20658-326">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="20658-327">受保护的浏览器存储实验性 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="20658-327">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="20658-328">ASP.NET Core 受保护的浏览器存储将 [ASP.NET Core 数据保护](xref:security/data-protection/introduction)用于 [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) 和 [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage)。</span><span class="sxs-lookup"><span data-stu-id="20658-328">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="20658-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` 是一个不受支持的实验性包，不适合用于生产。</span><span class="sxs-lookup"><span data-stu-id="20658-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="20658-330">此包仅适用于 ASP.NET Core 3.1 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="20658-330">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="20658-331">配置</span><span class="sxs-lookup"><span data-stu-id="20658-331">Configuration</span></span>

1. <span data-ttu-id="20658-332">将包引用添加到 [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)。</span><span class="sxs-lookup"><span data-stu-id="20658-332">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="20658-333">在 `Pages/_Host.cshtml` 文件中，将以下脚本添加到结束标记 `</body>` 之前：</span><span class="sxs-lookup"><span data-stu-id="20658-333">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="20658-334">在 `Startup.ConfigureServices` 中，调用 `AddProtectedBrowserStorage` 以将 `localStorage` 和 `sessionStorage` 服务添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="20658-334">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="20658-335">保存和加载组件中的数据</span><span class="sxs-lookup"><span data-stu-id="20658-335">Save and load data within a component</span></span>

<span data-ttu-id="20658-336">在需要将数据加载或保存到浏览器存储的任何组件中，使用 [`@inject`](xref:mvc/views/razor#inject) 指令注入以下任意一项的实例：</span><span class="sxs-lookup"><span data-stu-id="20658-336">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="20658-337">具体选择取决于要使用的浏览器存储位置。</span><span class="sxs-lookup"><span data-stu-id="20658-337">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="20658-338">在以下示例中，使用 `sessionStorage`：</span><span class="sxs-lookup"><span data-stu-id="20658-338">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="20658-339">可将 `@using` 语句放置在 `_Imports.razor` 文件而不是组件中。</span><span class="sxs-lookup"><span data-stu-id="20658-339">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="20658-340">使用 `_Imports.razor` 文件可使命名空间可用于应用的较大部分或整个应用。</span><span class="sxs-lookup"><span data-stu-id="20658-340">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="20658-341">若要在基于 Blazor Server 项目模板的应用的 `Counter` 组件中保留 `currentCount` 值，请修改 `IncrementCount` 方法以使用 `ProtectedSessionStore.SetAsync`：</span><span class="sxs-lookup"><span data-stu-id="20658-341">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="20658-342">在更大、更真实的应用中，存储单个字段是不太可能出现的情况。</span><span class="sxs-lookup"><span data-stu-id="20658-342">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="20658-343">应用更有可能存储包含复杂状态的整个模型对象。</span><span class="sxs-lookup"><span data-stu-id="20658-343">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="20658-344">`ProtectedSessionStore` 自动串行化和反序列化 JSON 数据。</span><span class="sxs-lookup"><span data-stu-id="20658-344">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="20658-345">在前面的代码示例中，`currentCount` 数据存储为用户浏览器中的 `sessionStorage['count']`。</span><span class="sxs-lookup"><span data-stu-id="20658-345">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="20658-346">数据不会以纯文本形式存储，而是使用 ASP.NET Core 的数据保护进行保护。</span><span class="sxs-lookup"><span data-stu-id="20658-346">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="20658-347">如果在浏览器的开发人员控制台中评估了 `sessionStorage['count']`，则可以检查已加密的数据。</span><span class="sxs-lookup"><span data-stu-id="20658-347">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="20658-348">若要在用户稍后返回到 `Counter` 组件时（包括他们位于全新线路上时）恢复 `currentCount` 数据，请使用 `ProtectedSessionStore.GetAsync`：</span><span class="sxs-lookup"><span data-stu-id="20658-348">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="20658-349">如果组件的参数包括导航状态，请调用 `ProtectedSessionStore.GetAsync` 并将结果分配给 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>，而不是 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="20658-349">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="20658-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 仅在首次实例化组件时调用一次。</span><span class="sxs-lookup"><span data-stu-id="20658-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="20658-351">如果用户导航到不同的 URL，而仍然停留在相同的页面上，则 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 之后不会再次调用。</span><span class="sxs-lookup"><span data-stu-id="20658-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="20658-352">有关详细信息，请参阅 <xref:blazor/components/lifecycle>。</span><span class="sxs-lookup"><span data-stu-id="20658-352">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="20658-353">本节中的示例仅在服务器未启用预呈现的情况下有效。</span><span class="sxs-lookup"><span data-stu-id="20658-353">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="20658-354">启用预呈现后，会生成错误，说明由于正在预呈现组件，无法发起 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="20658-354">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="20658-355">禁用预呈现或添加其他代码以处理预呈现。</span><span class="sxs-lookup"><span data-stu-id="20658-355">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="20658-356">若要了解有关编写可处理预呈现的代码的详细信息，请参阅[处理预呈现](#handle-prerendering)一节。</span><span class="sxs-lookup"><span data-stu-id="20658-356">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="20658-357">处理加载状态</span><span class="sxs-lookup"><span data-stu-id="20658-357">Handle the loading state</span></span>

<span data-ttu-id="20658-358">由于浏览器存储是异步访问（通过网络连接进行访问）的，因此往往需要一段时间才能加载完数据并可供组件使用。</span><span class="sxs-lookup"><span data-stu-id="20658-358">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="20658-359">为获得最佳结果，请在加载进行过程中呈现加载状态消息，而不要显示空数据或默认数据。</span><span class="sxs-lookup"><span data-stu-id="20658-359">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="20658-360">一种方法是跟踪数据是否为 `null`（表示数据仍在加载）。</span><span class="sxs-lookup"><span data-stu-id="20658-360">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="20658-361">在默认 `Counter` 组件中，计数保留在 `int` 中。</span><span class="sxs-lookup"><span data-stu-id="20658-361">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="20658-362">通过将问号 (`?`) 添加到类型 (`int`)，[使 `currentCount` 可以为 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)：</span><span class="sxs-lookup"><span data-stu-id="20658-362">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="20658-363">请勿无条件地显示计数和“`Increment`”按钮，而选择仅在数据已加载后才显示这些元素：</span><span class="sxs-lookup"><span data-stu-id="20658-363">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="20658-364">处理预呈现</span><span class="sxs-lookup"><span data-stu-id="20658-364">Handle prerendering</span></span>

<span data-ttu-id="20658-365">预呈现期间：</span><span class="sxs-lookup"><span data-stu-id="20658-365">During prerendering:</span></span>

* <span data-ttu-id="20658-366">与用户浏览器的交互式连接不存在。</span><span class="sxs-lookup"><span data-stu-id="20658-366">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="20658-367">浏览器尚无可在其中运行 JavaScript 代码的页面。</span><span class="sxs-lookup"><span data-stu-id="20658-367">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="20658-368">在预呈现期间，`localStorage` 或 `sessionStorage` 不可用。</span><span class="sxs-lookup"><span data-stu-id="20658-368">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="20658-369">如果组件尝试与存储进行交互，则会生成错误，说明由于正在预呈现组件，无法发起 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="20658-369">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="20658-370">解决此错误的一种方法是禁用预呈现。</span><span class="sxs-lookup"><span data-stu-id="20658-370">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="20658-371">如果应用大量使用基于浏览器的存储，则这通常是最佳选择。</span><span class="sxs-lookup"><span data-stu-id="20658-371">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="20658-372">预呈现会增加复杂性，且不会给应用带来好处，因为在 `localStorage` 或 `sessionStorage` 可用之前，应用无法预呈现任何有用的内容。</span><span class="sxs-lookup"><span data-stu-id="20658-372">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="20658-373">若要禁用预呈现，请打开 `Pages/_Host.cshtml` 文件，并将[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)的 `render-mode` 属性更改为 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>：</span><span class="sxs-lookup"><span data-stu-id="20658-373">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="20658-374">对于不使用 `localStorage` 或 `sessionStorage` 的其他页面，预呈现可能很有用。</span><span class="sxs-lookup"><span data-stu-id="20658-374">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="20658-375">若要保持预呈现状态，可延迟加载操作，直到浏览器连接到线路。</span><span class="sxs-lookup"><span data-stu-id="20658-375">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="20658-376">以下是存储计数器值的示例：</span><span class="sxs-lookup"><span data-stu-id="20658-376">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="20658-377">将状态保留提取到常见位置</span><span class="sxs-lookup"><span data-stu-id="20658-377">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="20658-378">如果多个组件依赖于基于浏览器的存储，则多次重新实现状态提供程序代码会造成代码重复。</span><span class="sxs-lookup"><span data-stu-id="20658-378">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="20658-379">若要避免代码重复，一种方法是创建一个封装了状态提供程序逻辑的状态提供程序父组件。</span><span class="sxs-lookup"><span data-stu-id="20658-379">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="20658-380">子组件可处理保留的数据，而无需考虑状态暂留机制。</span><span class="sxs-lookup"><span data-stu-id="20658-380">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="20658-381">在 `CounterStateProvider` 组件的以下示例中，将计数器数据保留到 `sessionStorage`：</span><span class="sxs-lookup"><span data-stu-id="20658-381">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="20658-382">`CounterStateProvider` 组件处理加载阶段的方式是在加载完成后才呈现其子内容。</span><span class="sxs-lookup"><span data-stu-id="20658-382">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="20658-383">若要使用 `CounterStateProvider` 组件，请围绕需要访问计数器状态的任何其他组件包装该组件的实例。</span><span class="sxs-lookup"><span data-stu-id="20658-383">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="20658-384">若要使某个应用中的所有组件都可以访问该状态，请围绕 `App` 组件 (`App.razor`) 中的 <xref:Microsoft.AspNetCore.Components.Routing.Router> 来包装 `CounterStateProvider` 组件：</span><span class="sxs-lookup"><span data-stu-id="20658-384">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="20658-385">已包装的组件接收并可修改保留的计数器状态。</span><span class="sxs-lookup"><span data-stu-id="20658-385">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="20658-386">以下 `Counter` 组件实现了该模式：</span><span class="sxs-lookup"><span data-stu-id="20658-386">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="20658-387">与 `ProtectedBrowserStorage` 进行交互无需前面的组件，该组件也不会处理“正在加载”阶段。</span><span class="sxs-lookup"><span data-stu-id="20658-387">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="20658-388">如前所述，若要处理预呈现，可对 `CounterStateProvider` 进行修改，以便所有使用计数器数据的组件均可自动处理预呈现。</span><span class="sxs-lookup"><span data-stu-id="20658-388">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="20658-389">有关详细信息，请参阅[处理预呈现](#handle-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="20658-389">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="20658-390">通常，建议在以下情况下使用状态提供程序父组件模式：</span><span class="sxs-lookup"><span data-stu-id="20658-390">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="20658-391">跨多个组件使用状态。</span><span class="sxs-lookup"><span data-stu-id="20658-391">To consume state across many components.</span></span>
* <span data-ttu-id="20658-392">只有一个顶级状态对象要保留时。</span><span class="sxs-lookup"><span data-stu-id="20658-392">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="20658-393">若要保留多个不同的状态对象并在不同位置使用不同的对象子集，最好避免全局保留状态。</span><span class="sxs-lookup"><span data-stu-id="20658-393">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

## <a name="in-memory-state-container-service"></a><span data-ttu-id="20658-394">内存中状态容器服务</span><span class="sxs-lookup"><span data-stu-id="20658-394">In-memory state container service</span></span>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

::: zone-end
