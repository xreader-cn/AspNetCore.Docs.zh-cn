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
ms.openlocfilehash: 7f147e29040b16966af1de8130ac36ff83c2a796
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551915"
---
<span data-ttu-id="d89cc-101">`Authentication` 组件 (`Pages/Authentication.razor`) 生成的页面定义了处理各种身份验证阶段所需的路由。</span><span class="sxs-lookup"><span data-stu-id="d89cc-101">The page produced by the `Authentication` component (`Pages/Authentication.razor`) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="d89cc-102"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 组件：</span><span class="sxs-lookup"><span data-stu-id="d89cc-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="d89cc-103">由 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="d89cc-103">Is provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="d89cc-104">管理在每个身份验证阶段执行适当的操作。</span><span class="sxs-lookup"><span data-stu-id="d89cc-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
