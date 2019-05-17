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
# <a name="host-and-deploy-blazor-server-side"></a>托管和部署 Blazor 服务器端

作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>主机配置值

使用[服务器端托管模型](xref:blazor/hosting-models#server-side)的服务器端应用可以接受[泛型主机配置值](xref:fundamentals/host/generic-host#host-configuration)。

## <a name="deployment"></a>部署

使用[服务器端托管模型](xref:blazor/hosting-models#server-side)，可在 ASP.NET Core 应用中的服务器上执行 Blazor。 UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。

能够托管 ASP.NET Core 应用的 Web 服务器是必需的。 Visual Studio 包括 Blazor（服务器端）项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `blazorserverside` 模板）。

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

## <a name="additional-resources"></a>其他资源

* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [将 ASP.NET Core 预览版部署到 Azure 应用服务](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
