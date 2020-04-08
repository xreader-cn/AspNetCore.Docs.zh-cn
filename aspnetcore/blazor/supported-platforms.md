---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor 支持的平台。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647106"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor 支持的平台

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>浏览器要求

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| 浏览器                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 当前               |
| Mozilla Firefox                  | 当前               |
| Google Chrome，包括 Android | 当前               |
| Safari，包括 iOS            | 当前               |
| Microsoft Internet Explorer      | 不支持&dagger; |

&dagger;Microsoft Internet Explorer 不支持 [WebAssembly](https://webassembly.org)。

### <a name="blazor-server"></a>Blazor 服务器

| 浏览器                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 当前    |
| Mozilla Firefox                  | 当前    |
| Google Chrome，包括 Android | 当前    |
| Safari，包括 iOS            | 当前    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;需要其他填充代码（例如，可通过 [Polyfill.io](https://polyfill.io/v3/) 捆绑包添加承诺）。

## <a name="additional-resources"></a>其他资源

* <xref:blazor/hosting-models>
