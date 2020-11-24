---
title: 托管和部署 ASP.NET Core Blazor Server
author: guardrex
description: 了解如何使用 ASP.NET Core 托管和部署 Blazor Server 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/server
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595436"
---
# <a name="host-and-deploy-no-locblazor-server"></a>托管和部署 Blazor Server

作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>主机配置值

[Blazor Server 应用](xref:blazor/hosting-models#blazor-server)可以接受[通用主机配置值](xref:fundamentals/host/generic-host#host-configuration)。

## <a name="deployment"></a>部署

使用 [Blazor Server 托管模型](xref:blazor/hosting-models#blazor-server)，可从 ASP.NET Core 应用中在服务器上执行 Blazor。 UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。

能够托管 ASP.NET Core 应用的 Web 服务器是必需的。 Visual Studio 包括“Blazor Server 应用”项目模板（使用 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令时为 `blazorserverside` 模板）。

## <a name="scalability"></a>可伸缩性

计划部署以将可用的基础设施充分用于 Blazor Server 应用。 请参阅以下资源来解决 Blazor Server 应用的可伸缩性：

* [Blazor Server 应用的基础知识](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a>部署服务器

考虑单一服务器（纵向扩展）的可伸缩性时，应用可用的内存可能是用户需求增加时应用将耗尽的第一个资源。 服务器上的可用内存影响以下因素：

* 服务器可以支持的主动电路数。
* 客户端上的 UI 延迟。

有关生成安全且可伸缩的 Blazor 服务器应用的指南，请参阅 <xref:blazor/security/server/threat-mitigation>。

每个电路使用约 250 KB 的内存来实现至少为 Hello World 样式的应用。 电路大小取决于应用代码和与每个组件相关的状态维护要求。 我们建议你在开发应用和基础设施的过程中衡量资源需求，但在计划部署目标时可以将以下基准作为起点：如果希望应用支持 5,000 个并发用户，请考虑为应用预算至少 1.3 GB 服务器内存（或每用户 ~273 KB）。

### <a name="no-locsignalr-configuration"></a>SignalR 配置

Blazor Server 应用使用 ASP.NET Core SignalR 与浏览器进行通信。 [SignalR 的托管和缩放条件](xref:signalr/publish-to-azure-web-app)适用于 Blazor Server 应用。

由于低延迟、可靠性和[安全性](xref:signalr/security)，使用 WebSocket 作为 SignalR 传输时，Blazor 的效果最佳。 当 WebSocket 不可用时，或在将应用显式配置为使用长轮询时，SignalR 将使用长轮询。 部署到 Azure 应用服务时，请在服务的 Azure 门户设置中将应用配置为使用 WebSocket。 有关为 Azure 应用服务配置应用的详细信息，请参阅 [SignalR 发布指南](xref:signalr/publish-to-azure-web-app)。

#### <a name="azure-no-locsignalr-service"></a>Azure SignalR 服务

我们建议将 [Azure SignalR 服务](xref:signalr/scale#azure-signalr-service)用于 Blazor Server 应用。 该服务允许将 Blazor Server 应用扩展到大量并发 SignalR 连接。 此外，SignalR 服务的全球覆盖和高性能数据中心可帮助显著减少由于地理位置造成的延迟。

> [!IMPORTANT]
> 禁用 [WebSocket](https://wikipedia.org/wiki/WebSocket) 后，Azure 应用服务使用 HTTP 长轮询模拟实时连接。 HTTP 长轮询明显比在启用 WebSocket 的情况下运行慢，WebSocket 不使用轮询来模拟客户机-服务器连接。
>
> 建议对部署到 Azure 应用服务的 Blazor Server 应用使用 WebSocket。 默认情况下，[Azure SignalR 服务](xref:signalr/scale#azure-signalr-service)使用 WebSockets。 如果应用不使用 Azure SignalR 服务，请参阅 <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>。
>
> 有关详细信息，请参阅：
>
> * [什么是 Azure SignalR 服务？](/azure/azure-signalr/signalr-overview)
> * [Azure SignalR 服务的性能指南](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a>Configuration

若要为 SignalR 服务配置应用，应用必须支持粘滞会话；在此情况下，客户端在[预呈现时被重定向回同一服务器](xref:blazor/hosting-models#connection-to-the-server)。 将 `ServerStickyMode` 选项或配置值设置为 `Required`。 通常，应用使用下述方法之一创建配置：

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * 配置（使用下述方法之一）：
  
     * 在 `appsettings.json`中：

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * Azure 门户中的应用服务“配置” > “应用程序设置”（名称：`Azure__SignalR__StickyServerMode`，值：`Required`）   。 如果[预配 Azure SignalR 服务](#provision-the-azure-signalr-service)，则为应用自动采用此方式。

### <a name="provision-the-azure-no-locsignalr-service"></a>预配 Azure SignalR 服务

若要在 Visual Studio 中为应用预配 Azure SignalR 服务：

1. 在 Visual Studio 中创建适用于 Blazor Server 应用的 Azure 应用发布配置文件。
1. 将 **Azure SignalR 服务** 依赖项添加到配置文件。 如果 Azure 订阅没有要分配给应用的预先存在的 Azure SignalR 服务实例，请选择“创建新的 Azure SignalR 服务实例”以预配新的服务实例。
1. 将应用发布到 Azure。

如果在 Visual Studio 中预配 Azure SignalR 服务，则会自动[启用粘滞会话](#configuration)，并将 SignalR 连接字符串添加到应用服务的配置中。

#### <a name="iis"></a>IIS

使用 IIS 时，请启用：

* [IIS 上的 WebSocket](xref:fundamentals/websockets#enabling-websockets-on-iis)。
* [具有应用程序请求路由的粘性会话](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)。

#### <a name="kubernetes"></a>Kubernetes

使用以下[粘滞会话的 Kubernetes 注释](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)创建入口定义：

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a>Linux 与 Nginx

要使 SignalR Websocket 正常运行，请确保将代理的 `Upgrade` 和 `Connection` 标头设置为以下值，并将 `$connection_upgrade` 映射到以下值之一：

* 默认情况下为升级标头值。
* 缺少升级标头或升级标头为空时为 `close`。

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

有关详细信息，请参阅以下文章：

* [NGINX 作为 WebSocket 代理](https://www.nginx.com/blog/websocket-nginx/)
* [WebSocket 代理](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a>Linux 与 Apache

若要在 Linux 上托管 Apache 后面的 Blazor 应用，请为 HTTP 和 WebSocket 流量配置 `ProxyPass`。

如下示例中：

* Kestrel 服务器正在主机上运行。
* 应用侦听端口 5000 上的流量。

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

启用以下模块：

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

检查浏览器控制台中的 WebSocket 错误。 示例错误：

* Firefox 无法通过 ws://the-domain-name.tld/_blazor?id=XXX 与服务器建立连接。
* 错误：未能启动传输“WebSocket”：错误：传输出现错误。
* 错误：未能启动传输“LongPolling”：TypeError：未定义 this.transport
* 错误：无法使用任何可用传输连接到服务器。 WebSocket 失败
* 错误：如果连接未处于“已连接”状态，则无法发送数据。

有关详细信息，请参阅 [Apache 文档](https://httpd.apache.org/docs/current/mod/mod_proxy.html)。

### <a name="measure-network-latency"></a>衡量网络延迟

可以使用 [JS 互操作](xref:blazor/call-javascript-from-dotnet)来衡量网络延迟，如以下示例所示：

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

为获得合理的 UI 体验，我们建议使用 250 毫秒或更低的持续 UI 延迟。
