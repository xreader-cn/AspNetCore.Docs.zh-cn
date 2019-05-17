---
title: 托管和部署 Blazor 服务器端
author: guardrex
description: 了解如何使用 ASP.NET Core 托管和部署 Blazor 服务器端应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/26/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8e44be09a4cceba2509f3e86abf3ce5fd2d077bd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887762"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="9d6d4-103">托管和部署 Blazor 服务器端</span><span class="sxs-lookup"><span data-stu-id="9d6d4-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="9d6d4-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9d6d4-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="9d6d4-105">主机配置值</span><span class="sxs-lookup"><span data-stu-id="9d6d4-105">Host configuration values</span></span>

<span data-ttu-id="9d6d4-106">使用[服务器端托管模型](xref:blazor/hosting-models#server-side)的服务器端应用可以接受[泛型主机配置值](xref:fundamentals/host/generic-host#host-configuration)。</span><span class="sxs-lookup"><span data-stu-id="9d6d4-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="9d6d4-107">部署</span><span class="sxs-lookup"><span data-stu-id="9d6d4-107">Deployment</span></span>

<span data-ttu-id="9d6d4-108">使用[服务器端托管模型](xref:blazor/hosting-models#server-side)，可在 ASP.NET Core 应用中的服务器上执行 Blazor。</span><span class="sxs-lookup"><span data-stu-id="9d6d4-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="9d6d4-109">UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。</span><span class="sxs-lookup"><span data-stu-id="9d6d4-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="9d6d4-110">能够托管 ASP.NET Core 应用的 Web 服务器是必需的。</span><span class="sxs-lookup"><span data-stu-id="9d6d4-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="9d6d4-111">Visual Studio 包括 Blazor（服务器端）项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `blazorserverside` 模板）。</span><span class="sxs-lookup"><span data-stu-id="9d6d4-111">Visual Studio includes the **Blazor (server-side)** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

## <a name="additional-resources"></a><span data-ttu-id="9d6d4-112">其他资源</span><span class="sxs-lookup"><span data-stu-id="9d6d4-112">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="9d6d4-113">将 ASP.NET Core 预览版部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="9d6d4-113">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
