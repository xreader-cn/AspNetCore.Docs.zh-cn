---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor的支持平台。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/supported-platforms
ms.openlocfilehash: de51296cc8785474e1c1406cfd5d4e5bd4050172
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962729"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a>ASP.NET Core Blazor 支持的平台

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>浏览器需求

### <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

| 浏览者                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 当前               |
| Mozilla Firefox                  | 当前               |
| Google Chrome，包括 Android | 当前               |
| Safari，包括 iOS            | 当前               |
| Microsoft Internet Explorer      | 不支持&dagger; |

&dagger;Microsoft Internet Explorer 不支持[WebAssembly](https://webassembly.org)。

### <a name="opno-locblazor-server"></a>Blazor 服务器

| 浏览者                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 当前    |
| Mozilla Firefox                  | 当前    |
| Google Chrome，包括 Android | 当前    |
| Safari，包括 iOS            | 当前    |
| Microsoft Internet Explorer      | 11&dagger; |

需要 &dagger;其他填充代码（例如，可通过[Polyfill.io](https://polyfill.io/v3/)捆绑添加承诺）。

## <a name="additional-resources"></a>其他资源

* <xref:blazor/hosting-models>
