---
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
ms.openlocfilehash: b6f6bc2e094c9070e0ea57b507f558313f19bc15
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551796"
---
ASP.NET Core [Identity](xref:security/authentication/identity) 在很大程度上不受 [SameSite cookie ](xref:security/samesite) 的影响，如或集成的高级方案除外 `IFrames` `OpenIdConnect` 。

使用时 `Identity` ，请 ***不要*** 添加任何 cookie 提供程序或调用 ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` ，而是执行此操作 `Identity` 。