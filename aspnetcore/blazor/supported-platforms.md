---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor 的受支持平台。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 4e86bd6967a747a59c99a515c1c838cc2c21770f
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391218"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor 支持的平台

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>浏览器需求

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| 浏览者                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 当前               |
| Mozilla Firefox                  | 当前               |
| Google Chrome，包括 Android | 当前               |
| Safari，包括 iOS            | 当前               |
| Microsoft Internet Explorer      | 不支持 @ no__t-0 |

@no__t 0Microsoft Internet Explorer 不支持[WebAssembly](https://webassembly.org)。

### <a name="blazor-server"></a>Blazor 服务器

| 浏览者                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 当前    |
| Mozilla Firefox                  | 当前    |
| Google Chrome，包括 Android | 当前    |
| Safari，包括 iOS            | 当前    |
| Microsoft Internet Explorer      | 11 @ no__t-0 |

需要 @no__t 0Additional 填充代码（例如，可通过[Polyfill.io](https://polyfill.io/v3/)捆绑添加承诺）。

## <a name="additional-resources"></a>其他资源

* <xref:blazor/hosting-models>
