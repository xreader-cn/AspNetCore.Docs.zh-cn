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
ms.openlocfilehash: 164c9e8f73502fc627c4514bd683dc183d318b1d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551901"
---
通过“Reset”，服务器可以使用指定的错误代码重置 HTTP/2 请求。 重置请求被视为中止。

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

前述代码示例中的 `Reset` 指定 `INTERNAL_ERROR` 错误代码。 有关 HTTP/2 错误代码的详细信息，请访问[“HTTP/2 规范错误代码”部分](https://tools.ietf.org/html/rfc7540#page-50)。