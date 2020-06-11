---
title: 在 Web 场中托管 ASP.NET Core
author: rick-anderson
description: 了解如何在 Web 场环境中托管包含共享资源的 ASP.NET Core 应用的多个实例。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/web-farm
ms.openlocfilehash: 37a50fa2939eb31e66925fa907212d54577bd20b
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106567"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>在 Web 场中托管 ASP.NET Core

作者：[Chris Ross](https://github.com/Tratcher)

Web 场包含两个或多个 Web 服务器（亦称为“节点”），用于托管应用的多个实例。 若有用户请求到达 Web 场，负载均衡器会将请求分发到 Web 场中的各个节点。 Web 场提高了：

* 可靠性/可用性：如果一个或多个节点失败，负载均衡器可以将请求路由到其他正常运行的节点，以继续处理请求。
* 容量/性能：多个节点可以处理的请求数多于一个服务器。 负载均衡器均衡工作负载的方式是，将请求分发到各个节点。
* **可伸缩性**：如果需要更多或更少容量，可以增加或减少活动节点数，与工作负载保持一致。 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)等 Web 场平台技术可以应系统管理员的请求自动添加或删除节点，也可以自动开始，而无需人为干预。
* 可维护性：Web 场节点可以依赖一组共享服务，这就简化了系统管理。 例如，Web 场中的节点可以依赖单一数据库服务器，以及静态资源（如图像和可下载文件）的公用网络位置。

本主题介绍了在 Web 场中托管且依赖共享资源的 ASP.NET Core 应用的配置和依赖项。

## <a name="general-configuration"></a>常规配置

<xref:host-and-deploy/index>  
了解如何设置托管环境和部署 ASP.NET Core 应用。 对 Web 场中的每个节点配置进程管理器，以自动启动和重启应用。 每个节点都需要 ASP.NET Core 运行时。 有关详细信息，请参阅文档的[托管和部署](xref:host-and-deploy/index)区域中的主题。

<xref:host-and-deploy/proxy-load-balancer>  
了解在代理服务器和负载均衡器后方托管的应用程序的配置，这通常会隐藏重要的请求信息。

<xref:host-and-deploy/azure-apps/index>  
[Azure 应用服务](https://azure.microsoft.com/services/app-service/)是一个用于托管 Web 应用（包括 ASP.NET Core）的 [Microsoft 云计算平台服务](https://azure.microsoft.com/)。 应用服务是提供自动缩放、负载均衡、修补和持续部署的完全托管平台。

## <a name="app-data"></a>应用数据

如果应用已缩放为多个实例，可能会有需要跨节点共享的应用状态。 若为暂时状态，建议共享 [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache)。 如果需要暂留共享状态，建议在数据库中存储共享状态。

## <a name="required-configuration"></a>必需配置

必需为部署到 Web 场的应用配置数据保护和缓存。

### <a name="data-protection"></a>数据保护

应用使用 [ASP.NET Core 数据保护系统](xref:security/data-protection/introduction)来保护数据。 数据保护系统依赖一组在密钥环中存储的加密密钥。 初始化后，数据保护系统会应用在本地存储密钥环的[默认设置](xref:security/data-protection/configuration/default-settings)。 根据默认配置，唯一密钥环存储在 Web 场的各个节点上。 因此，Web 场中的每个节点都无法解密应用在其他任何节点上加密的数据。 默认配置通常不适合在 Web 场中托管应用。 若要实现共享密钥环，可以改为始终将用户请求路由到相同的节点。 若要详细了解与 Web 场部署有关的数据保护系统配置，请参阅<xref:security/data-protection/configuration/overview>。

### <a name="caching"></a>缓存

在 Web 场环境中，缓存机制必须跨 Web 场中的节点共享缓存项。 缓存必须依赖公用 Redis 缓存、共享 SQL Server 数据库，或跨 Web 场共享缓存项的自定义缓存实现。 有关详细信息，请参阅 <xref:performance/caching/distributed>。

## <a name="dependent-components"></a>依赖组件

下面的方案无需其他配置，但依赖需要配置 Web 场的技术。

| 方案 | 依赖&hellip; |
| -------- | ------------------- |
| 身份验证 | 数据保护（请参阅<xref:security/data-protection/configuration/overview>）。<br><br>有关详细信息，请参阅 <xref:security/authentication/cookie> 和 <xref:security/cookie-sharing>。 |
| Identity | 身份验证和数据库配置。<br><br>有关详细信息，请参阅 <xref:security/authentication/identity>。 |
| 会话 | 数据保护（加密 Cookie）（请参阅<xref:security/data-protection/configuration/overview>）和缓存（请参阅<xref:performance/caching/distributed>）。<br><br>有关详细信息，请参阅[会话和状态管理：会话状态](xref:fundamentals/app-state#session-state)。 |
| TempData | 数据保护（加密 Cookie）（请参阅 <xref:security/data-protection/configuration/overview>）或会话（请参阅[会话和状态管理：会话状态](xref:fundamentals/app-state#session-state)）。<br><br>有关详细信息，请参阅[会话和状态管理：TempData](xref:fundamentals/app-state#tempdata)。 |
| 防伪造 | 数据保护（请参阅<xref:security/data-protection/configuration/overview>）。<br><br>有关详细信息，请参阅 <xref:security/anti-request-forgery>。 |

## <a name="troubleshoot"></a>疑难解答

### <a name="data-protection-and-caching"></a>数据保护和缓存

如果未为 Web 场环境配置数据保护或缓存，就会在处理请求时发生间歇性错误。 之所以会发生这种情况是因为，节点不共享相同的资源，并且用户请求并不总是路由回同一节点。

假设用户通过 Cookie 身份验证来登录应用。 用户在 Web 场中的一个节点上登录应用。 如果用户的下一个请求到达登录应用时所用的同一节点，应用便能解密身份验证 Cookie，并允许用户访问应用资源。 如果用户的下一个请求到达其他节点，应用便无法从用户登录时所用的节点解密身份验证 Cookie，并且无法授权用户请求获取的资源。

如果以下任一症状间歇性出现，问题原因通常是为 Web 场环境配置的数据保护或缓存不正确：

* 身份验证中断：身份验证 Cookie 配置不正确或无法解密。 OAuth（Facebook、Microsoft、Twitter）或 OpenIdConnect 登录失败，出现错误“关联失败”。
* 授权中断：Identity 丢失。
* 会话状态丢失数据。
* 缓存项消失。
* TempData 失败。
* POST 失败：防伪造检查失败。

若要详细了解与 Web 场部署有关的数据保护配置，请参阅<xref:security/data-protection/configuration/overview>。 若要详细了解与 Web 场部署有关的缓存配置，请参阅<xref:performance/caching/distributed>。

## <a name="obtain-data-from-apps"></a>从应用中获取数据

如果 Web 场应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。 有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。

## <a name="additional-resources"></a>其他资源

* [适用于 Windows 的自定义脚本扩展](/azure/virtual-machines/extensions/custom-script-windows)：在 Azure 虚拟机上下载和执行脚本，这对于部署后配置和软件安装很有用。
* <xref:host-and-deploy/proxy-load-balancer>
 