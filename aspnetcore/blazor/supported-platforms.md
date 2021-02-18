---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor 支持的平台。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280725"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor 支持的平台

::: moniker range=">= aspnetcore-5.0"

下表中所示的浏览器支持 Blazor WebAssembly 和 Blazor Server。

| 浏览者                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari，包括 iOS      | 当前&dagger; |
| Google Chrome，包括 Android | 当前&dagger; |
| Microsoft Edge                   | 当前&dagger; |
| Mozilla Firefox                  | 当前&dagger; |  

&dagger;最新指的是浏览器的最新版本。  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| 浏览者                          | Version               |
| -------------------------------- | --------------------- |
| Apple Safari，包括 iOS      | 当前&dagger;       |
| Google Chrome，包括 Android | 当前&dagger;       |
| Microsoft Edge                   | 当前&dagger;       |
| Microsoft Internet Explorer      | 不支持&Dagger; |
| Mozilla Firefox                  | 当前&dagger;       |  

&dagger;最新指的是浏览器的最新版本。  
&Dagger;Microsoft Internet Explorer 不支持 [WebAssembly](https://webassembly.org)。

## Blazor Server

| 浏览者                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari，包括 iOS      | 当前&dagger; |
| Google Chrome，包括 Android | 当前&dagger; |
| Microsoft Edge                   | 当前&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox                  | 当前&dagger; |

&dagger;最新指的是浏览器的最新版本。  
&Dagger;需要其他填充代码。 例如，可通过 [`Polyfill.io`](https://polyfill.io/v3/) 捆绑包添加承诺。

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
