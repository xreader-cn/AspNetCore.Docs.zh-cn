---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor 的受支持平台。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 01f3a55a8536feedf713e07ea3724a0bc51e7c63
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "68948247"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor 支持的平台

作者：[Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>浏览器需求

### <a name="blazor-client-side"></a>Blazor 客户端

| 浏览者                          | 版本               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 当前               |
| Mozilla Firefox                  | 当前               |
| Google Chrome, 包括 Android | 当前               |
| Safari, 包括 iOS            | 当前               |
| Microsoft Internet Explorer      | 不支持&dagger; |

&dagger;Microsoft Internet Explorer 不支持[WebAssembly](https://webassembly.org)。

### <a name="blazor-server-side"></a>Razor 服务器端

| 浏览者                          | 版本    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 当前    |
| Mozilla Firefox                  | 当前    |
| Google Chrome, 包括 Android | 当前    |
| Safari, 包括 iOS            | 当前    |
| Microsoft Internet Explorer      | 11x17&dagger; |

&dagger;需要额外的填充代码 (例如, 可通过[Polyfill.io](https://polyfill.io/v3/)捆绑添加承诺)。

## <a name="additional-resources"></a>其他资源

* <xref:blazor/hosting-models>
