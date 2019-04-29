---
title: 托管和部署 Blazor 服务器端
author: guardrex
description: 了解如何使用 ASP.NET Core 托管和部署 Blazor 服务器端应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 940020ee44d72d50395aad64bc924413c1bbecfb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614632"
---
# <a name="host-and-deploy-blazor-server-side"></a>托管和部署 Blazor 服务器端

作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>主机配置值

使用[服务器端托管模型](xref:blazor/hosting-models#server-side-hosting-model)的服务器端应用可以接受[泛型主机配置值](xref:fundamentals/host/generic-host#host-configuration)。

## <a name="deployment"></a>部署

使用[服务器端托管模型](xref:blazor/hosting-models#server-side-hosting-model)，可在 ASP.NET Core 应用中的服务器上执行 Blazor。 UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。

ASP.NET Core 应用在已发布输出中随附此应用，所以这两个应用是一起部署的。 需要能够托管 ASP.NET Core 应用的 Web 服务器。 对于服务器端部署，Visual Studio 包括“Blazor 组件”项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `razorcomponents` 模板）。

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。

若要了解如何部署到 Azure 应用服务，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。
