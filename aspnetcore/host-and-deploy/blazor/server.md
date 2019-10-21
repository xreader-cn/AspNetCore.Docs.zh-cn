---
title: 托管和部署 ASP.NET Core Blazor 服务器
author: guardrex
description: 了解如何使用 ASP.NET Core 托管和部署 Blazor 服务器应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 693d7ff67bad3a0c5bd050b795833763056ed511
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378813"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="c5a11-103">托管和部署 Blazor 服务器</span><span class="sxs-lookup"><span data-stu-id="c5a11-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="c5a11-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c5a11-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="c5a11-105">主机配置值</span><span class="sxs-lookup"><span data-stu-id="c5a11-105">Host configuration values</span></span>

<span data-ttu-id="c5a11-106">[Blazor 服务器应用](xref:blazor/hosting-models#blazor-server)可以接受[通用主机配置值](xref:fundamentals/host/generic-host#host-configuration)。</span><span class="sxs-lookup"><span data-stu-id="c5a11-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="c5a11-107">部署</span><span class="sxs-lookup"><span data-stu-id="c5a11-107">Deployment</span></span>

<span data-ttu-id="c5a11-108">使用 [Blazor 服务器托管模型](xref:blazor/hosting-models#blazor-server)，可从 ASP.NET Core 应用中在服务器上执行 Blazor。</span><span class="sxs-lookup"><span data-stu-id="c5a11-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="c5a11-109">UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。</span><span class="sxs-lookup"><span data-stu-id="c5a11-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="c5a11-110">能够托管 ASP.NET Core 应用的 Web 服务器是必需的。</span><span class="sxs-lookup"><span data-stu-id="c5a11-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="c5a11-111">Visual Studio 包括“Blazor 服务器应用”  项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `blazorserverside` 模板）。</span><span class="sxs-lookup"><span data-stu-id="c5a11-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="c5a11-112">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="c5a11-112">Scalability</span></span>

<span data-ttu-id="c5a11-113">计划部署以将可用的基础设施充分用于 Blazor 服务器应用。</span><span class="sxs-lookup"><span data-stu-id="c5a11-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="c5a11-114">请参阅以下资源来解决 Blazor 服务器应用的可伸缩性：</span><span class="sxs-lookup"><span data-stu-id="c5a11-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="c5a11-115">Blazor 服务器应用的基础知识</span><span class="sxs-lookup"><span data-stu-id="c5a11-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="c5a11-116">部署服务器</span><span class="sxs-lookup"><span data-stu-id="c5a11-116">Deployment server</span></span>

<span data-ttu-id="c5a11-117">考虑单一服务器（纵向扩展）的可伸缩性时，应用可用的内存可能是用户需求增加时应用将耗尽的第一个资源。</span><span class="sxs-lookup"><span data-stu-id="c5a11-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="c5a11-118">服务器上的可用内存影响以下因素：</span><span class="sxs-lookup"><span data-stu-id="c5a11-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="c5a11-119">服务器可以支持的主动电路数。</span><span class="sxs-lookup"><span data-stu-id="c5a11-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="c5a11-120">客户端上的 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="c5a11-120">UI latency on the client.</span></span>

<span data-ttu-id="c5a11-121">有关生成安全且可伸缩的 Blazor 服务器应用的指南，请参阅 <xref:security/blazor/server>。</span><span class="sxs-lookup"><span data-stu-id="c5a11-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="c5a11-122">每个电路使用约 250 KB 的内存来实现至少为 Hello World  样式的应用。</span><span class="sxs-lookup"><span data-stu-id="c5a11-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="c5a11-123">电路大小取决于应用代码和与每个组件相关的状态维护要求。</span><span class="sxs-lookup"><span data-stu-id="c5a11-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="c5a11-124">我们建议你在开发应用和基础设施的过程中衡量资源需求，但在计划部署目标时可以将以下基准作为起点：如果希望应用支持 5,000 个并发用户，请考虑为应用预算至少 1.3 GB 服务器内存（或每用户 ~273 KB）。</span><span class="sxs-lookup"><span data-stu-id="c5a11-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="c5a11-125">SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="c5a11-125">SignalR configuration</span></span>

<span data-ttu-id="c5a11-126">Blazor Server 应用使用 ASP.NET Core SignalR 与浏览器进行通信。</span><span class="sxs-lookup"><span data-stu-id="c5a11-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="c5a11-127">[SignalR 的托管和缩放条件](xref:signalr/publish-to-azure-web-app)适用于 Blazor 服务器应用。</span><span class="sxs-lookup"><span data-stu-id="c5a11-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="c5a11-128">由于低延迟、可靠性和[安全性](xref:signalr/security)，使用 WebSocket 作为 SignalR 传输时，Blazor 的效果最佳。</span><span class="sxs-lookup"><span data-stu-id="c5a11-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="c5a11-129">当 WebSocket 不可用时，或在将应用显式配置为使用长轮询时，SignalR 将使用长轮询。</span><span class="sxs-lookup"><span data-stu-id="c5a11-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="c5a11-130">部署到 Azure 应用服务时，请在服务的 Azure 门户设置中将应用配置为使用 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="c5a11-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="c5a11-131">有关为 Azure 应用服务配置应用的详细信息，请参阅 [SignalR 发布指南](xref:signalr/publish-to-azure-web-app)。</span><span class="sxs-lookup"><span data-stu-id="c5a11-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

<span data-ttu-id="c5a11-132">我们建议将 [Azure SignalR 服务](/azure/azure-signalr)用于 Blazor 服务器应用。</span><span class="sxs-lookup"><span data-stu-id="c5a11-132">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="c5a11-133">该服务允许将 Blazor 服务器应用扩展到大量并发 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="c5a11-133">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="c5a11-134">此外，SignalR 服务的全球覆盖和高性能数据中心可帮助显著减少由于地理位置造成的延迟。</span><span class="sxs-lookup"><span data-stu-id="c5a11-134">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="c5a11-135">若要配置应用（并选择性地预配），Azure SignalR 服务应：</span><span class="sxs-lookup"><span data-stu-id="c5a11-135">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

* <span data-ttu-id="c5a11-136">在 Visual Studio 中创建适用于 Blazor 服务器应用的 Azure 应用发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="c5a11-136">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
* <span data-ttu-id="c5a11-137">将 Azure SignalR 服务  依赖项添加到配置文件。</span><span class="sxs-lookup"><span data-stu-id="c5a11-137">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="c5a11-138">如果 Azure 订阅没有要分配给应用的预先存在的 Azure SignalR 服务实例，请选择“创建新的 Azure SignalR 服务实例”  以预配新的服务实例。</span><span class="sxs-lookup"><span data-stu-id="c5a11-138">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
* <span data-ttu-id="c5a11-139">将应用发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="c5a11-139">Publish the app to Azure.</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="c5a11-140">衡量网络延迟</span><span class="sxs-lookup"><span data-stu-id="c5a11-140">Measure network latency</span></span>

<span data-ttu-id="c5a11-141">可以使用 [JS 互操作](xref:blazor/javascript-interop)来衡量网络延迟，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="c5a11-141">[JS interop](xref:blazor/javascript-interop) can be used to measure network latency, as the following example demonstrates:</span></span>

```cshtml
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="c5a11-142">为获得合理的 UI 体验，我们建议使用 250 毫秒或更低的持续 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="c5a11-142">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
