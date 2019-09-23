---
title: 托管和部署 ASP.NET Core Blazor 服务器
author: guardrex
description: 了解如何使用 ASP.NET Core 托管和部署 Blazor 服务器应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: host-and-deploy/blazor/server
ms.openlocfilehash: a393d620924d847e674a09972515a8130a15fc6a
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70964226"
---
# <a name="host-and-deploy-blazor-server"></a>托管和部署 Blazor 服务器

作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>主机配置值

[Blazor 服务器应用](xref:blazor/hosting-models#blazor-server)可以接受[通用主机配置值](xref:fundamentals/host/generic-host#host-configuration)。

## <a name="deployment"></a>部署

使用 [Blazor 服务器托管模型](xref:blazor/hosting-models#blazor-server)，可从 ASP.NET Core 应用中在服务器上执行 Blazor。 UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。

能够托管 ASP.NET Core 应用的 Web 服务器是必需的。 Visual Studio 包括“Blazor 服务器应用”  项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `blazorserverside` 模板）。

## <a name="scalability"></a>可伸缩性

计划部署以将可用的基础设施充分用于 Blazor 服务器应用。 请参阅以下资源来解决 Blazor 服务器应用的可伸缩性：

* [Blazor 服务器应用的基础知识](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>部署服务器

考虑单一服务器（纵向扩展）的可伸缩性时，应用可用的内存可能是用户需求增加时应用将耗尽的第一个资源。 服务器上的可用内存影响以下因素：

* 服务器可以支持的主动电路数。
* 客户端上的 UI 延迟。

有关生成安全且可伸缩的 Blazor 服务器应用的指南，请参阅 <xref:security/blazor/server>。

每个电路使用约 250 KB 的内存来实现至少为 Hello World  样式的应用。 电路大小取决于应用代码和与每个组件相关的状态维护要求。 我们建议你在开发应用和基础设施的过程中衡量资源需求，但在计划部署目标时可以将以下基准作为起点：如果希望应用支持 5,000 个并发用户，请考虑为应用预算至少 1.3 GB 服务器内存（或每用户 ~273 KB）。

### <a name="signalr-configuration"></a>SignalR 配置

Blazor Server 应用使用 ASP.NET Core SignalR 与浏览器进行通信。 [SignalR 的托管和缩放条件](xref:signalr/publish-to-azure-web-app)适用于 Blazor 服务器应用。

由于低延迟、可靠性和[安全性](xref:signalr/security)，使用 WebSocket 作为 SignalR 传输时，Blazor 的效果最佳。 当 WebSocket 不可用时，或在将应用显式配置为使用长轮询时，SignalR 将使用长轮询。 部署到 Azure 应用服务时，请在服务的 Azure 门户设置中将应用配置为使用 WebSocket。 有关为 Azure 应用服务配置应用的详细信息，请参阅 [SignalR 发布指南](xref:signalr/publish-to-azure-web-app)。

我们建议将 [Azure SignalR 服务](/azure/azure-signalr)用于 Blazor 服务器应用。 该服务允许将 Blazor 服务器应用扩展到大量并发 SignalR 连接。 此外，SignalR 服务的全球覆盖和高性能数据中心可帮助显著减少由于地理位置造成的延迟。

### <a name="measure-network-latency"></a>衡量网络延迟

可以使用 [JS 互操作](xref:blazor/javascript-interop)来衡量网络延迟，如以下示例所示：

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

为获得合理的 UI 体验，我们建议使用 250 毫秒或更低的持续 UI 延迟。
