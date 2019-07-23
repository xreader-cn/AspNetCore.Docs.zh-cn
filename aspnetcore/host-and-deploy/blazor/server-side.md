---
title: 托管和部署 ASP.NET Core Blazor 服务器端
author: guardrex
description: 了解如何使用 ASP.NET Core 托管和部署 Blazor 服务器端应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 56a03ff583bf85497e2b3bacc70123845a046e3d
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67892700"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="869cb-103">托管和部署 Blazor 服务器端</span><span class="sxs-lookup"><span data-stu-id="869cb-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="869cb-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="869cb-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="869cb-105">主机配置值</span><span class="sxs-lookup"><span data-stu-id="869cb-105">Host configuration values</span></span>

<span data-ttu-id="869cb-106">使用[服务器端托管模型](xref:blazor/hosting-models#server-side)的服务器端应用可以接受[泛型主机配置值](xref:fundamentals/host/generic-host#host-configuration)。</span><span class="sxs-lookup"><span data-stu-id="869cb-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="869cb-107">部署</span><span class="sxs-lookup"><span data-stu-id="869cb-107">Deployment</span></span>

<span data-ttu-id="869cb-108">使用[服务器端托管模型](xref:blazor/hosting-models#server-side)，可在 ASP.NET Core 应用中的服务器上执行 Blazor。</span><span class="sxs-lookup"><span data-stu-id="869cb-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="869cb-109">UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。</span><span class="sxs-lookup"><span data-stu-id="869cb-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="869cb-110">能够托管 ASP.NET Core 应用的 Web 服务器是必需的。</span><span class="sxs-lookup"><span data-stu-id="869cb-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="869cb-111">Visual Studio 包括“Blazor 服务器应用”  项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `blazorserverside` 模板）。</span><span class="sxs-lookup"><span data-stu-id="869cb-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="connection-scale-out"></a><span data-ttu-id="869cb-112">连接横向扩展</span><span class="sxs-lookup"><span data-stu-id="869cb-112">Connection scale out</span></span>

<span data-ttu-id="869cb-113">Blazor 服务器端应用要求，必须为每个用户提供一个活动 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="869cb-113">Blazor server-side apps require one active SignalR connection for each user.</span></span> <span data-ttu-id="869cb-114">生产 Blazor 服务器端部署要求，解决方案必须支持应用所需的任意多个并发连接。</span><span class="sxs-lookup"><span data-stu-id="869cb-114">A production Blazor server-side deployment requires a solution for supporting as many concurrent connections as required by the app.</span></span> <span data-ttu-id="869cb-115">[Azure SignalR 服务](/azure/azure-signalr/)处理连接缩放，建议将此服务用作 Blazor 服务器端应用的缩放解决方案。</span><span class="sxs-lookup"><span data-stu-id="869cb-115">The [Azure SignalR Service](/azure/azure-signalr/) handles the scaling of connections and is recommended as a scaling solution for Blazor server-side apps.</span></span> <span data-ttu-id="869cb-116">有关详细信息，请参阅 <xref:signalr/publish-to-azure-web-app>。</span><span class="sxs-lookup"><span data-stu-id="869cb-116">For more information, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="signalr-configuration"></a><span data-ttu-id="869cb-117">SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="869cb-117">SignalR configuration</span></span>

<span data-ttu-id="869cb-118">SignalR 是由 ASP.NET Core 配置，用于最常见的 Blazor 服务器端方案。</span><span class="sxs-lookup"><span data-stu-id="869cb-118">SignalR is configured by ASP.NET Core for the most common Blazor server-side scenarios.</span></span> <span data-ttu-id="869cb-119">对于自定义和高级方案，请参阅[其他资源](#additional-resources)部分中的 SignalR 文章。</span><span class="sxs-lookup"><span data-stu-id="869cb-119">For custom and advanced scenarios, consult the SignalR articles in the [Additional resources](#additional-resources) section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="869cb-120">其他资源</span><span class="sxs-lookup"><span data-stu-id="869cb-120">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="869cb-121">Azure SignalR 服务文档</span><span class="sxs-lookup"><span data-stu-id="869cb-121">Azure SignalR Service Documentation</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="869cb-122">快速入门：使用 SignalR 服务创建聊天室</span><span class="sxs-lookup"><span data-stu-id="869cb-122">Quickstart: Create a chat room by using SignalR Service</span></span>](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="869cb-123">将 ASP.NET Core 预览版部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="869cb-123">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
