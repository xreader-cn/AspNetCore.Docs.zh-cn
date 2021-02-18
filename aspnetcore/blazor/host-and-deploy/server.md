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
ms.openlocfilehash: 75682171a59a610a24364778616774c49257d2ad
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279846"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="65120-103">托管和部署 Blazor Server</span><span class="sxs-lookup"><span data-stu-id="65120-103">Host and deploy Blazor Server</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="65120-104">主机配置值</span><span class="sxs-lookup"><span data-stu-id="65120-104">Host configuration values</span></span>

<span data-ttu-id="65120-105">[Blazor Server 应用](xref:blazor/hosting-models#blazor-server)可以接受[通用主机配置值](xref:fundamentals/host/generic-host#host-configuration)。</span><span class="sxs-lookup"><span data-stu-id="65120-105">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="65120-106">部署</span><span class="sxs-lookup"><span data-stu-id="65120-106">Deployment</span></span>

<span data-ttu-id="65120-107">使用 [Blazor Server 托管模型](xref:blazor/hosting-models#blazor-server)，可从 ASP.NET Core 应用中在服务器上执行 Blazor。</span><span class="sxs-lookup"><span data-stu-id="65120-107">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="65120-108">UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。</span><span class="sxs-lookup"><span data-stu-id="65120-108">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="65120-109">能够托管 ASP.NET Core 应用的 Web 服务器是必需的。</span><span class="sxs-lookup"><span data-stu-id="65120-109">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="65120-110">Visual Studio 包括“Blazor Server 应用”项目模板（使用 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令时为 `blazorserverside` 模板）。</span><span class="sxs-lookup"><span data-stu-id="65120-110">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="65120-111">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="65120-111">Scalability</span></span>

<span data-ttu-id="65120-112">计划部署以将可用的基础设施充分用于 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="65120-112">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="65120-113">请参阅以下资源来解决 Blazor Server 应用的可伸缩性：</span><span class="sxs-lookup"><span data-stu-id="65120-113">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="65120-114">Blazor Server 应用的基础知识</span><span class="sxs-lookup"><span data-stu-id="65120-114">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="65120-115">部署服务器</span><span class="sxs-lookup"><span data-stu-id="65120-115">Deployment server</span></span>

<span data-ttu-id="65120-116">考虑单一服务器（纵向扩展）的可伸缩性时，应用可用的内存可能是用户需求增加时应用将耗尽的第一个资源。</span><span class="sxs-lookup"><span data-stu-id="65120-116">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="65120-117">服务器上的可用内存影响以下因素：</span><span class="sxs-lookup"><span data-stu-id="65120-117">The available memory on the server affects the:</span></span>

* <span data-ttu-id="65120-118">服务器可以支持的主动电路数。</span><span class="sxs-lookup"><span data-stu-id="65120-118">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="65120-119">客户端上的 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="65120-119">UI latency on the client.</span></span>

<span data-ttu-id="65120-120">有关生成安全且可伸缩的 Blazor 服务器应用的指南，请参阅 <xref:blazor/security/server/threat-mitigation>。</span><span class="sxs-lookup"><span data-stu-id="65120-120">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="65120-121">每个电路使用约 250 KB 的内存来实现至少为 Hello World 样式的应用。</span><span class="sxs-lookup"><span data-stu-id="65120-121">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="65120-122">电路大小取决于应用代码和与每个组件相关的状态维护要求。</span><span class="sxs-lookup"><span data-stu-id="65120-122">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="65120-123">我们建议你在开发应用和基础设施的过程中衡量资源需求，但在计划部署目标时可以将以下基准作为起点：如果希望应用支持 5,000 个并发用户，请考虑为应用预算至少 1.3 GB 服务器内存（或每用户 ~273 KB）。</span><span class="sxs-lookup"><span data-stu-id="65120-123">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="65120-124">SignalR 配置</span><span class="sxs-lookup"><span data-stu-id="65120-124">SignalR configuration</span></span>

<span data-ttu-id="65120-125">Blazor Server 应用使用 ASP.NET Core SignalR 与浏览器进行通信。</span><span class="sxs-lookup"><span data-stu-id="65120-125">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="65120-126">[SignalR 的托管和缩放条件](xref:signalr/publish-to-azure-web-app)适用于 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="65120-126">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="65120-127">由于低延迟、可靠性和[安全性](xref:signalr/security)，使用 WebSocket 作为 SignalR 传输时，Blazor 的效果最佳。</span><span class="sxs-lookup"><span data-stu-id="65120-127">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="65120-128">当 WebSocket 不可用时，或在将应用显式配置为使用长轮询时，SignalR 将使用长轮询。</span><span class="sxs-lookup"><span data-stu-id="65120-128">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="65120-129">部署到 Azure 应用服务时，请在服务的 Azure 门户设置中将应用配置为使用 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="65120-129">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="65120-130">有关为 Azure 应用服务配置应用的详细信息，请参阅 [SignalR 发布指南](xref:signalr/publish-to-azure-web-app)。</span><span class="sxs-lookup"><span data-stu-id="65120-130">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="65120-131">Azure SignalR 服务</span><span class="sxs-lookup"><span data-stu-id="65120-131">Azure SignalR Service</span></span>

<span data-ttu-id="65120-132">我们建议将 [Azure SignalR 服务](xref:signalr/scale#azure-signalr-service)用于 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="65120-132">We recommend using the [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps.</span></span> <span data-ttu-id="65120-133">该服务允许将 Blazor Server 应用扩展到大量并发 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="65120-133">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="65120-134">此外，SignalR 服务的全球覆盖和高性能数据中心可帮助显著减少由于地理位置造成的延迟。</span><span class="sxs-lookup"><span data-stu-id="65120-134">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="65120-135">禁用 [WebSocket](https://wikipedia.org/wiki/WebSocket) 后，Azure 应用服务使用 HTTP 长轮询模拟实时连接。</span><span class="sxs-lookup"><span data-stu-id="65120-135">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="65120-136">HTTP 长轮询明显比在启用 WebSocket 的情况下运行慢，WebSocket 不使用轮询来模拟客户机-服务器连接。</span><span class="sxs-lookup"><span data-stu-id="65120-136">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="65120-137">建议对部署到 Azure 应用服务的 Blazor Server 应用使用 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="65120-137">We recommend using WebSockets for Blazor Server apps deployed to Azure App Service.</span></span> <span data-ttu-id="65120-138">默认情况下，[Azure SignalR 服务](xref:signalr/scale#azure-signalr-service)使用 WebSockets。</span><span class="sxs-lookup"><span data-stu-id="65120-138">The [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="65120-139">如果应用不使用 Azure SignalR 服务，请参阅 <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>。</span><span class="sxs-lookup"><span data-stu-id="65120-139">If the app doesn't use the Azure SignalR Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="65120-140">有关详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="65120-140">For more information, see:</span></span>
>
> * [<span data-ttu-id="65120-141">什么是 Azure SignalR 服务？</span><span class="sxs-lookup"><span data-stu-id="65120-141">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="65120-142">Azure SignalR 服务的性能指南</span><span class="sxs-lookup"><span data-stu-id="65120-142">Performance guide for Azure SignalR Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="65120-143">Configuration</span><span class="sxs-lookup"><span data-stu-id="65120-143">Configuration</span></span>

<span data-ttu-id="65120-144">若要为 SignalR 服务配置应用，应用必须支持粘滞会话；在此情况下，客户端在[预呈现时被重定向回同一服务器](xref:blazor/hosting-models#connection-to-the-server)。</span><span class="sxs-lookup"><span data-stu-id="65120-144">To configure an app for the Azure SignalR Service, the app must support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="65120-145">将 `ServerStickyMode` 选项或配置值设置为 `Required`。</span><span class="sxs-lookup"><span data-stu-id="65120-145">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="65120-146">通常，应用使用下述方法之一创建配置：</span><span class="sxs-lookup"><span data-stu-id="65120-146">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="65120-147">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="65120-147">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="65120-148">配置（使用下述方法之一）：</span><span class="sxs-lookup"><span data-stu-id="65120-148">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="65120-149">在 `appsettings.json`中：</span><span class="sxs-lookup"><span data-stu-id="65120-149">In `appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * <span data-ttu-id="65120-150">Azure 门户中的应用服务“配置” > “应用程序设置”（名称：`Azure__SignalR__StickyServerMode`，值：`Required`）   。</span><span class="sxs-lookup"><span data-stu-id="65120-150">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure__SignalR__StickyServerMode`, **Value**: `Required`).</span></span> <span data-ttu-id="65120-151">如果[预配 Azure SignalR 服务](#provision-the-azure-signalr-service)，则为应用自动采用此方式。</span><span class="sxs-lookup"><span data-stu-id="65120-151">This approach is adopted for the app automatically if you [provision the Azure SignalR Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-signalr-service"></a><span data-ttu-id="65120-152">预配 Azure SignalR 服务</span><span class="sxs-lookup"><span data-stu-id="65120-152">Provision the Azure SignalR Service</span></span>

<span data-ttu-id="65120-153">若要在 Visual Studio 中为应用预配 Azure SignalR 服务：</span><span class="sxs-lookup"><span data-stu-id="65120-153">To provision the Azure SignalR Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="65120-154">在 Visual Studio 中创建适用于 Blazor Server 应用的 Azure 应用发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="65120-154">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="65120-155">将 **Azure SignalR 服务** 依赖项添加到配置文件。</span><span class="sxs-lookup"><span data-stu-id="65120-155">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="65120-156">如果 Azure 订阅没有要分配给应用的预先存在的 Azure SignalR 服务实例，请选择“创建新的 Azure SignalR 服务实例”以预配新的服务实例。</span><span class="sxs-lookup"><span data-stu-id="65120-156">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="65120-157">将应用发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="65120-157">Publish the app to Azure.</span></span>

<span data-ttu-id="65120-158">如果在 Visual Studio 中预配 Azure SignalR 服务，则会自动[启用粘滞会话](#configuration)，并将 SignalR 连接字符串添加到应用服务的配置中。</span><span class="sxs-lookup"><span data-stu-id="65120-158">Provisioning the Azure SignalR Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the SignalR connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="65120-159">IIS</span><span class="sxs-lookup"><span data-stu-id="65120-159">IIS</span></span>

<span data-ttu-id="65120-160">使用 IIS 时，请启用：</span><span class="sxs-lookup"><span data-stu-id="65120-160">When using IIS, enable:</span></span>

* <span data-ttu-id="65120-161">[IIS 上的 WebSocket](xref:fundamentals/websockets#enabling-websockets-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="65120-161">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="65120-162">[具有应用程序请求路由的粘性会话](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)。</span><span class="sxs-lookup"><span data-stu-id="65120-162">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="65120-163">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="65120-163">Kubernetes</span></span>

<span data-ttu-id="65120-164">使用以下[粘滞会话的 Kubernetes 注释](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)创建入口定义：</span><span class="sxs-lookup"><span data-stu-id="65120-164">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="65120-165">Linux 与 Nginx</span><span class="sxs-lookup"><span data-stu-id="65120-165">Linux with Nginx</span></span>

<span data-ttu-id="65120-166">要使 SignalR Websocket 正常运行，请确保将代理的 `Upgrade` 和 `Connection` 标头设置为以下值，并将 `$connection_upgrade` 映射到以下值之一：</span><span class="sxs-lookup"><span data-stu-id="65120-166">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="65120-167">默认情况下为升级标头值。</span><span class="sxs-lookup"><span data-stu-id="65120-167">The Upgrade header value by default.</span></span>
* <span data-ttu-id="65120-168">缺少升级标头或升级标头为空时为 `close`。</span><span class="sxs-lookup"><span data-stu-id="65120-168">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="65120-169">有关详细信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="65120-169">For more information, see the following articles:</span></span>

* [<span data-ttu-id="65120-170">NGINX 作为 WebSocket 代理</span><span class="sxs-lookup"><span data-stu-id="65120-170">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="65120-171">WebSocket 代理</span><span class="sxs-lookup"><span data-stu-id="65120-171">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="65120-172">Linux 与 Apache</span><span class="sxs-lookup"><span data-stu-id="65120-172">Linux with Apache</span></span>

<span data-ttu-id="65120-173">若要在 Linux 上托管 Apache 后面的 Blazor 应用，请为 HTTP 和 WebSocket 流量配置 `ProxyPass`。</span><span class="sxs-lookup"><span data-stu-id="65120-173">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="65120-174">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="65120-174">In the following example:</span></span>

* <span data-ttu-id="65120-175">Kestrel 服务器正在主机上运行。</span><span class="sxs-lookup"><span data-stu-id="65120-175">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="65120-176">应用侦听端口 5000 上的流量。</span><span class="sxs-lookup"><span data-stu-id="65120-176">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="65120-177">启用以下模块：</span><span class="sxs-lookup"><span data-stu-id="65120-177">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="65120-178">检查浏览器控制台中的 WebSocket 错误。</span><span class="sxs-lookup"><span data-stu-id="65120-178">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="65120-179">示例错误：</span><span class="sxs-lookup"><span data-stu-id="65120-179">Example errors:</span></span>

* <span data-ttu-id="65120-180">Firefox 无法通过 ws://the-domain-name.tld/_blazor?id=XXX 与服务器建立连接。</span><span class="sxs-lookup"><span data-stu-id="65120-180">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="65120-181">错误：未能启动传输“WebSocket”：错误：传输出现错误。</span><span class="sxs-lookup"><span data-stu-id="65120-181">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="65120-182">错误：未能启动传输“LongPolling”：TypeError：未定义 this.transport</span><span class="sxs-lookup"><span data-stu-id="65120-182">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="65120-183">错误：无法使用任何可用传输连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="65120-183">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="65120-184">WebSocket 失败</span><span class="sxs-lookup"><span data-stu-id="65120-184">WebSockets failed</span></span>
* <span data-ttu-id="65120-185">错误：如果连接未处于“已连接”状态，则无法发送数据。</span><span class="sxs-lookup"><span data-stu-id="65120-185">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="65120-186">有关详细信息，请参阅 [Apache 文档](https://httpd.apache.org/docs/current/mod/mod_proxy.html)。</span><span class="sxs-lookup"><span data-stu-id="65120-186">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="65120-187">衡量网络延迟</span><span class="sxs-lookup"><span data-stu-id="65120-187">Measure network latency</span></span>

<span data-ttu-id="65120-188">可以使用 [JS 互操作](xref:blazor/call-javascript-from-dotnet)来衡量网络延迟，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="65120-188">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="65120-189">为获得合理的 UI 体验，我们建议使用 250 毫秒或更低的持续 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="65120-189">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
