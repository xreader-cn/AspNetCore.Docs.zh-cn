---
title: 在 ASP.NET 4.x 和 ASP.NET Core 之间进行选择
author: rick-anderson
description: 介绍 ASP.NET Core 和ASP.NET 4.x 以及如何在它们之间进行选择。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
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
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: e0b0311622a841183433363a55fcf33e22b6b115
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633469"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>在 ASP.NET 4.x 和 ASP.NET Core 之间进行选择

ASP.NET Core 是 ASP.NET 4.x 的重新设计。 本文列出了两者之间的区别。

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core 是一个跨平台的开源框架，用于在 Windows、macOS 或 Linux 上生成基于云的新式 Web 应用。

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x 是一个成熟的框架，提供在 Windows 上生成基于服务器的企业级 Web 应用所需的服务。

## <a name="framework-selection"></a>框架选择

下表将 ASP.NET Core 与 ASP.NET 4.x 进行比较。

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|针对 Windows、macOS 或 Linux 进行生成|针对 Windows 进行生成|
|[Razor Pages](xref:razor-pages/index) 是在 ASP.NET Core 2.x 及更高版本中创建 Web UI 时建议使用的方法。 另请参阅 [MVC](xref:mvc/overview)、[Web API](xref:tutorials/first-web-api) 和 [SignalR](xref:signalr/introduction)。|使用 [Web Forms](/aspnet/web-forms)、[SignalR](/aspnet/signalr)、[MVC](/aspnet/mvc)、[Web API](/aspnet/web-api/)、[WebHook](/aspnet/webhooks/) 或[网页](/aspnet/web-pages)|
|每个计算机多个版本|每个计算机一个版本|
|使用 C# 或 F# 通过 [Visual Studio](https://visualstudio.microsoft.com/vs/)、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) 或 [Visual Studio Code](https://code.visualstudio.com/) 进行开发|使用 C#、VB 或 F# 通过 [Visual Studio](https://visualstudio.microsoft.com/vs/) 进行开发|
|比 ASP.NET 4.x 性能更高|良好的性能|
|[使用 .NET Core 运行时](/dotnet/standard/choosing-core-framework-server)|使用 .NET Framework 运行时|

有关 .NET Framework 上的 ASP.NET Core 2.x 支持的信息，请参阅[面向 .NET Framework 的 ASP.NET Core](xref:index#target-framework)。

## <a name="aspnet-core-scenarios"></a>ASP.NET Core 方案

* [网站](xref:tutorials/first-mvc-app/index)
* [API](xref:tutorials/first-web-api)
* [实时](xref:signalr/introduction)
* [将 ASP.NET Core 应用部署到 Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET 4.x 方案

* [网站](/aspnet/mvc)
* [API](/aspnet/web-api)
* [实时](/aspnet/signalr)
* [在 Azure 中创建 ASP.NET 4.x Web 应用](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>其他资源

* [ASP.NET 简介](/aspnet/overview)
* [ASP.NET Core 简介](xref:index)
* <xref:host-and-deploy/azure-apps/index>
