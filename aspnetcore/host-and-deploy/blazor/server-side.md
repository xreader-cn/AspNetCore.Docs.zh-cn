---
title: 托管和部署 ASP.NET Core Blazor 服务器端
author: guardrex
description: 了解如何使用 ASP.NET Core 托管和部署 Blazor 服务器端应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8b332c2fb439e9832d604ed26f972b266eed2507
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406123"
---
# <a name="host-and-deploy-blazor-server-side"></a>托管和部署 Blazor 服务器端

作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>主机配置值

使用[服务器端托管模型](xref:blazor/hosting-models#server-side)的服务器端应用可以接受[泛型主机配置值](xref:fundamentals/host/generic-host#host-configuration)。

## <a name="deployment"></a>部署

使用[服务器端托管模型](xref:blazor/hosting-models#server-side)，可在 ASP.NET Core 应用中的服务器上执行 Blazor。 UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。

能够托管 ASP.NET Core 应用的 Web 服务器是必需的。 Visual Studio 包括 Blazor（服务器端）  项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `blazorserverside` 模板）。

## <a name="connection-scale-out"></a>连接横向扩展

Blazor 服务器端应用要求，必须为每个用户提供一个活动 SignalR 连接。 生产 Blazor 服务器端部署要求，解决方案必须支持应用所需的任意多个并发连接。 [Azure SignalR 服务](/azure/azure-signalr/)处理连接缩放，建议将此服务用作 Blazor 服务器端应用的缩放解决方案。 有关详细信息，请参阅 <xref:signalr/publish-to-azure-web-app>。

## <a name="signalr-configuration"></a>SignalR 配置

SignalR 是由 ASP.NET Core 配置，用于最常见的 Blazor 服务器端方案。 对于自定义和高级方案，请参阅[其他资源](#additional-resources)部分中的 SignalR 文章。

## <a name="additional-resources"></a>其他资源

* <xref:signalr/introduction>
* [Azure SignalR 服务文档](/azure/azure-signalr/)
* [快速入门：使用 SignalR 服务创建聊天室](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [将 ASP.NET Core 预览版部署到 Azure 应用服务](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
