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
ms.openlocfilehash: ae5d8cf64c0db9402a5457cec1df8402f4db103c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552174"
---
<span data-ttu-id="bd9d4-101">Azure 门户根据 AAD 租户是否有[已验证或未验证的发布者域](/azure/active-directory/develop/howto-configure-publisher-domain)提供以下应用 ID URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="bd9d4-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="bd9d4-102">如果在客户端应用中使用前述两个应用 ID URI 中的后一个来形成作用域 URI，并且授权不成功，请尝试提供没有方案和主机的作用域 URI：</span><span class="sxs-lookup"><span data-stu-id="bd9d4-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="bd9d4-103">示例：</span><span class="sxs-lookup"><span data-stu-id="bd9d4-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
