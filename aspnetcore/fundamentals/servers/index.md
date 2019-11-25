---
title: ASP.NET Core 中的 Web 服务器实现
author: rick-anderson
description: 发现适用于 ASP.NET Core 的 Web 服务器 Kestrel 和 HTTP.sys。 了解如何选择服务器以及何时使用反向代理服务器。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/servers/index
ms.openlocfilehash: d46793ef54c99fe609b5983c5a658fb7b20032fa
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289065"
---
# <a name="web-server-implementations-in-aspnet-core"></a>ASP.NET Core 中的 Web 服务器实现

作者：[Tom Dykstra](https://github.com/tdykstra)、 [Steve Smith](https://ardalis.com/)、[Stephen Halter](https://twitter.com/halter73) 和 [Chris Ross](https://github.com/Tratcher)

ASP.NET Core 应用与进程内 HTTP 服务器实现一起运行。 该服务器实现侦听 HTTP 请求，并以组成 <xref:Microsoft.AspNetCore.Http.HttpContext> 的[请求功能](xref:fundamentals/request-features)集形式，将它们呈现给应用。

## <a name="kestrel"></a>Kestrel

Kestrel 是 ASP.NET Core 项目模板指定的默认 Web 服务器。

使用 Kestrel：

* 本身作为边缘服务器，处理直接来自网络（包括 Internet）的请求。

  ![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

* 与反向代理服务器  （如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。 反向代理服务器接收来自 Internet 的 HTTP 请求，并将这些请求转发到 Kestrel。

  ![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

无论托管配置是否使用反向代理服务器，都是受支持的托管配置。

有关 Kestrel 配置指南和何时在反向代理配置中使用 Kestrel 的信息，请参阅 <xref:fundamentals/servers/kestrel>。

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core 随附以下组件：

* [Kestrel 服务器](xref:fundamentals/servers/kestrel)是默认跨平台 HTTP 服务器实现。
* IIS HTTP 服务器是 IIS 的[进程内服务器](#hosting-models)。
* [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)是仅用于 Windows 的 HTTP 服务器，它基于 [HTTP.sys 核心驱动程序和 HTTP 服务器 API](/windows/desktop/Http/http-api-start-page)。

使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用会在以下其中一个进程中运行：

* 在使用 IIS HTTP 服务器的 IIS 工作进程（[进程内托管模型](#hosting-models)）相同的进程中。 “进程内”  建议的配置。
* 在独立于 IIS 工作进程（[进程外托管模型](#hosting-models)）和 [Kestrel 服务器](#kestrel)的进程中。

[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)是本机 IIS 模块，用于处理 IIS 和进程内 IIS HTTP 服务器或 Kestrel 之间的本机 IIS 请求。 有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。

## <a name="hosting-models"></a>托管模型

使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。 进程内承载相较进程外承载提供更优的性能，因为请求并不通过环回适配器进行代理，环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。 IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。

通过进程外托管，ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，而由模块来处理进程管理。 该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。 这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。

有关详细信息和配置指南，请参阅以下主题：

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core 随附 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，这是默认跨平台 HTTP 服务器。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core 随附 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，这是默认跨平台 HTTP 服务器。

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core 随附以下组件：

* [Kestrel 服务器](xref:fundamentals/servers/kestrel)是默认跨平台 HTTP 服务器。
* [HTTP.sys 服务器](xref:fundamentals/servers/httpsys)是仅用于 Windows 的 HTTP 服务器，它基于 [HTTP.sys 核心驱动程序和 HTTP 服务器 API](/windows/desktop/Http/http-api-start-page)。

使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用在独立于 IIS 工作进程（进程外）  和 [Kestrel 服务器](#kestrel)的进程中运行。

由于 ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，因此该模块会处理进程管理。 该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。 这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。

下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：

![ASP.NET Core 模块](_static/ancm-outofprocess.png)

请求从 Web 到达内核模式 HTTP.sys 驱动程序。 驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。 该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。

该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。 执行其他检查，拒绝不是来自该模块的请求。 该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。

Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。 中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。 IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。 应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。

有关 IIS 和 ASP.NET Core 模块的配置指南，请参阅以下主题：

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core 随附 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，这是默认跨平台 HTTP 服务器。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core 随附 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，这是默认跨平台 HTTP 服务器。

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx 与 Kestrel

若要了解如何在 Linux 上使用 Nginx 作为 Kestrel 的反向代理服务器，请参阅 <xref:host-and-deploy/linux-nginx>。

### <a name="apache-with-kestrel"></a>Apache 与 Kestrel

若要了解如何在 Linux 上使用 Apache 作为 Kestrel 的反向代理服务器，请参阅 <xref:host-and-deploy/linux-apache>。

## <a name="httpsys"></a>HTTP.sys

如果 ASP.NET Core 应用在 Windows 上运行，则 HTTP.sys 是 Kestrel 的替代选项。 为了获得最佳性能，通常建议使用 Kestrel。 在应用向 Internet 公开且所需功能受 HTTP.sys（而不是 Kestrel）支持的方案中，可以使用 HTTP.sys。 有关详细信息，请参阅 <xref:fundamentals/servers/httpsys>。

![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

对于仅向内部网络公开的应用，HTTP.sys 同样适用。

![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

有关 HTTP.sys 的配置指南，请参阅 <xref:fundamentals/servers/httpsys>。

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core 服务器基础结构

`Startup.Configure` 方法中提供的 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 公开了类型 <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> 的 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> 属性。 Kestrel 和 HTTP.sys 各自仅公开单个功能，即 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>，但是不同的服务器实现可能公开其他功能。

`IServerAddressesFeature` 可用于查找服务器实现在运行时绑定的端口。

## <a name="custom-servers"></a>自定义服务器

如果内置服务器无法满足应用需求，可以创建一个自定义服务器实现。 [.NET 的开放 Web 接口 (OWIN) 指南](xref:fundamentals/owin) 演示了如何编写基于 [Nowin](https://github.com/Bobris/Nowin) 的 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实现。 只有应用使用的功能接口需要实现，但至少必须支持 <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> 和 <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>。

## <a name="server-startup"></a>服务器启动

集成开发环境 (IDE) 或编辑器启动以下应用时，会启动服务器：

* [Visual Studio](https://visualstudio.microsoft.com) &ndash; 可使用启动配置文件通过 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)或控制台来启动应用和服务器。
* [Visual Studio Code](https://code.visualstudio.com/) &ndash; 由[Omnisharp](https://github.com/OmniSharp/omnisharp-vscode) 通过激活 CoreCLR 调试程序来启动应用和服务器。
* [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; 由 [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) 启动应用和服务器。

从项目文件夹中的命令提示符启动应用时，[dotnet run](/dotnet/core/tools/dotnet-run) 会启动该应用和服务器（仅 Kestrel 和 HTTP.sys）。 可通过 `-c|--configuration` 选项指定此配置，该选项设置为 `Debug`（默认值）或 `Release`。

使用 `dotnet run` 或使用工具中内置的调试程序（如 Visual Studio）启动应用时，launchSettings.json 文件会提供配置  。 如果启动配置文件位于 launchSettings.json 文件中，请结合使用 `--launch-profile {PROFILE NAME}` 选项和 `dotnet run` 命令或在 Visual Studio 中选择配置文件  。 有关详细信息，请参阅 [dotnet run](/dotnet/core/tools/dotnet-run) 和 [.NET Core 分发打包](/dotnet/core/build/distribution-packaging)。

## <a name="http2-support"></a>HTTP/2 支持

以下部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * 操作系统
    * Windows Server 2016/Windows 10 或更高版本&dagger;
    * 具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）
    * macOS 的未来版本将支持 HTTP/2。
  * 目标框架：.NET Core 2.2 或更高版本
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 或更高版本
  * 目标框架：不适用于 HTTP.sys 部署。
* [IIS（进程内）](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本
  * 目标框架：.NET Core 2.2 或更高版本
* [IIS（进程外）](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本
  * 面向公众的边缘服务器连接使用 HTTP/2，但与 Kestrel 的反向代理连接使用 HTTP/1.1。
  * 目标框架：不适用于 IIS 进程外部署。

&dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。 支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。 可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 或更高版本
  * 目标框架：不适用于 HTTP.sys 部署。
* [IIS（进程外）](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本
  * 面向公众的边缘服务器连接使用 HTTP/2，但与 Kestrel 的反向代理连接使用 HTTP/1.1。
  * 目标框架：不适用于 IIS 进程外部署。

::: moniker-end

HTTP/2 连接必须使用[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 和 TLS 1.2 或更高版本。 有关详细信息，请参阅与服务器部署方案相关的主题。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
