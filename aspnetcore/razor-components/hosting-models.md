---
title: 承载模型的 razor 组件
author: guardrex
description: 了解客户端 Blazor 和服务器端 ASP.NET Core Razor 组件承载模型。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: razor-components/hosting-models
ms.openlocfilehash: 8ed70117c94bf1a3e4c208f70310bbf0473bae44
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515413"
---
# <a name="razor-components-hosting-models"></a>承载模型的 razor 组件

通过[Daniel Roth](https://github.com/danroth27)

Razor 组件是一个 web 框架，用于运行客户端上的浏览器中[WebAssembly](http://webassembly.org/)-基于.NET 运行时 (*Blazor*) 或 ASP.NET Core 中的服务器端 (*ASP.NET Core Razor组件*)。 无论托管模型、 应用和组件模型*保持不变*。 本文讨论了可用的托管模型：

* [客户端 Blazor](#client-side-hosting-model)
* [服务器端 ASP.NET Core Razor 组件](#server-side-hosting-model)

## <a name="client-side-hosting-model"></a>客户端托管模型

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Blazor 的主体托管模型是 WebAssembly 上的浏览器中的正在运行客户端。 将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。 应用将在浏览器线程中直接执行。 UI 更新和事件处理发生在同一进程内。 应用程序的资产部署到 web 服务器或服务能够向客户端提供静态内容的静态文件的方式。

![Blazor 客户端：在浏览器在 UI 线程上运行 Blazor 应用。](hosting-models/_static/client-side.png)

若要创建一个 Blazor 应用使用客户端的托管模型，请使用以下模板之一：

* **Blazor** ([dotnet 新 blazor](/dotnet/core/tools/dotnet-new))&ndash;部署为一组静态文件。
* **（ASP.NET Core 托管） 的 Blazor** ([dotnet 新 blazorhosted](/dotnet/core/tools/dotnet-new))&ndash;由 ASP.NET Core 服务器托管。 ASP.NET Core 应用提供给客户端 Blazor 应用。 与服务器进行交互的客户端 Blazor 应用程序可以通过使用 web API 调用网络或[SignalR](xref:signalr/introduction)。

这些模板包含*components.webassembly.js*处理的脚本：

* 下载.NET 运行时、 应用和应用程序的依赖关系。
* 若要运行该应用程序的运行时初始化。

客户端的托管模型提供了以下几个好处。 客户端 Blazor:

* 不具有任何.NET 服务器端依赖项。
* 充分利用客户端资源和功能。
* 卸载可从服务器到客户端。
* 支持脱机方案。

有客户端托管的弊端。 客户端 Blazor:

* 将应用程序限制为浏览器的功能。
* 需要支持客户端硬件和软件 （例如，WebAssembly 支持）。
* 具有更大的下载大小和加载时间较长的应用。
* 更小成熟的.NET 运行时和工具支持 (例如中的限制[.NET Standard](/dotnet/standard/net-standard)支持和调试)。

## <a name="server-side-hosting-model"></a>服务器端托管模型

使用 ASP.NET Core Razor 组件服务器端的托管模型，在服务器上从 ASP.NET Core 应用中执行应用程序。 通过处理 UI 更新、 事件处理和 JavaScript 调用[SignalR](xref:signalr/introduction)连接。

![ASP.NET Core Razor 组件服务器端：在浏览器与交互 （托管在 ASP.NET Core 应用内） 的应用的服务器上通过 SignalR 连接。](hosting-models/_static/server-side.png)

若要创建一个 Razor 组件应用使用服务器端的托管模型，使用 ASP.NET Core **Razor 组件**模板 ([dotnet 新 razorcomponents](/dotnet/core/tools/dotnet-new))。 ASP.NET Core 应用托管 Razor 组件服务器端应用程序，并设置 SignalR 终结点，客户端连接。 ASP.NET Core 应用引用应用程序的`Startup`要添加类：

* 服务器端 Razor 组件服务。
* 在应用请求处理管道中。

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

*Components.server.js*脚本&dagger;建立客户端连接。 它是应用程序负责保持和还原应用程序状态，根据需要 （例如，发生时丢失的网络连接）。

服务器端的托管模型提供了以下几个好处。 服务器端 Razor 组件：

* 比客户端 Blazor 应用程序的显著变小应用程序大小和速度更快加载。
* 可以充分利用的服务器功能，其中包括使用任何.NET Core 兼容的 Api。
* .NET Core 上运行的服务器上，因此现有的.NET 工具，如调试，按预期方式工作。
* 适用于瘦客户端 （例如，浏览器不支持 WebAssembly 和资源约束设备）。
* .NET /C#的基本代码，包括应用程序的组件代码不提供给客户端。

有服务器端托管的弊端。 服务器端 Razor 组件：

* 具有较高的延迟：每个用户交互涉及到的网络跃点。
* 产品/服务没有脱机支持：如果客户端连接失败，应用将停止工作。
* 减少可伸缩性：服务器必须管理多个客户端连接，并处理客户端状态。
* 需要 ASP.NET Core 服务器来提供应用。 不使用 （例如，从 CDN) 的服务器的部署不能。

&dagger;*Components.server.js*脚本发布到以下路径： *bin / {调试 |版本} / {目标框架} /publish/ {应用程序名称}。应用/dist/框架 （_f)*。
