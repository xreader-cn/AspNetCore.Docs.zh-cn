---
title: .NET 上的 gRPC 支持的平台
author: jamesnk
description: 了解 .NET 上的 gRPC 支持的平台。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530159"
---
# <a name="grpc-on-net-supported-platforms"></a>.NET 上的 gRPC 支持的平台

作者：[James Newton-King](https://twitter.com/jamesnk)

本文讨论在 .NET 中使用 gRPC 的要求和支持的平台。

gRPC 充分利用 HTTP/2 中提供的高级功能。 在任何位置都不支持 HTTP/2，但使用 HTTP/1.1 的第二种线路格式可用于 gRPC：

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - HTTP/2 上的 gRPC 是 gRPC 的常见使用方式。
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web 修改 gRPC 协议，使其与 HTTP/1.1 兼容。 gRPC-Web 可用于更多位置，尤其是它可由浏览器应用调用。 不再支持两个高级 gRPC 功能：客户端流式处理和双向流式处理。

.NET 上的 gRPC 支持两种线路格式。 默认情况下，使用 HTTP/2 上的 gRPC。 有关设置 gRPC-Web 的信息，请参阅 <xref:grpc/browser>。

## <a name="device-requirements"></a>设备要求

.NET 上的 gRPC 支持 .NET Core 支持的任何设备。

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * 浏览器&Dagger;

&dagger;[macOS 不支持通过 HTTPS 托管 ASP.NET Core 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。 macOS 上的 gRPC 客户端可以调用使用 HTTPS 的远程服务。

&Dagger;Blazor WebAssembly 应用可以使用 gRPC-Web 调用 gRPC 服务。

## <a name="aspnet-core-server-requirements"></a>ASP.NET Core 服务器要求

gRPC 服务可在所有内置 ASP.NET Core 服务器上托管。

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS 需要 .NET 5 和 Windows 10 内部版本 20241 或更高版本。

&Dagger;HTTP.sys 需要 .NET 5 和 Windows 10 内部版本 19529 或更高版本。

有关配置 ASP.NET Core 服务器以运行 gRPC 的信息，请参阅 <xref:grpc/aspnetcore#server-options>。

## <a name="net-version-requirements"></a>.NET 版本要求

.NET 上的 gRPC 支持 .NET Core 3 和 .NET 5 或更高版本。

> [!div class="checklist"]
>
> * .NET 5 或更高版本
> * .NET Core 3

.NET 上的 gRPC 不支持在 .NET Framework 和 Xamarin 上运行。 [gRPC C# 代码库](https://grpc.io/docs/languages/csharp/quickstart/)是支持 .NET Framework 和 Xamarin 的第三方库。 Microsoft 不支持 gRPC C-core。

## <a name="azure-services"></a>Azure 服务

> [!div class="checklist"]
>
> * [Azure Kubernetes 服务 (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure 应用服务](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure 应用服务不支持通过 HTTP/2 托管 gRPC。 gRPC-Web 是一种兼容的替代方法。

工作正在进行，以在 Azure 应用服务中提高 HTTP/2 对 gRPC 的支持。 有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/9020)。

## <a name="additional-resources"></a>其他资源

* [gRPC C# 核心库](https://grpc.io/docs/languages/csharp/quickstart/)
