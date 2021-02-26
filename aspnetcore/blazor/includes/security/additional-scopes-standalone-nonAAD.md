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
ms.openlocfilehash: 72192957383af94838d8279d116acfc949f5594f
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551258"
---
<span data-ttu-id="6753f-101">为 `openid` 和 `offline_access` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> 添加一对 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>：</span><span class="sxs-lookup"><span data-stu-id="6753f-101">Add a pair of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `openid` and `offline_access` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("openid");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("offline_access");
});
```
