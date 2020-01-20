---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor的支持平台。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160127"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a>ASP.NET Core Blazor 支持的平台

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>浏览器需求

### <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

| 浏览器                          | {2&gt;版本&lt;2}               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 当前               |
| Mozilla Firefox                  | 当前               |
| Google Chrome，包括 Android | 当前               |
| Safari，包括 iOS            | 当前               |
| Microsoft Internet Explorer      | 不支持&dagger; |

&dagger;Microsoft Internet Explorer 不支持[WebAssembly](https://webassembly.org)。

### <a name="opno-locblazor-server"></a>Blazor 服务器

| 浏览器                          | {2&gt;版本&lt;2}    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 当前    |
| Mozilla Firefox                  | 当前    |
| Google Chrome，包括 Android | 当前    |
| Safari，包括 iOS            | 当前    |
| Microsoft Internet Explorer      | 11&dagger; |

需要 &dagger;其他填充代码（例如，可通过[Polyfill.io](https://polyfill.io/v3/)捆绑添加承诺）。

## <a name="additional-resources"></a>其他资源

* <xref:blazor/hosting-models>
