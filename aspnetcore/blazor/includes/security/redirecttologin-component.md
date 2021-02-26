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
ms.openlocfilehash: 9b7b302485a5c9ab7d1b1da6ce4d8ab7d1c26f9c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552788"
---
<span data-ttu-id="ef811-101">`RedirectToLogin` 组件 (`Shared/RedirectToLogin.razor`)：</span><span class="sxs-lookup"><span data-stu-id="ef811-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="ef811-102">管理将未经授权的用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="ef811-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="ef811-103">保留用户尝试访问的当前 URL，以便在身份验证成功时可以将其返回到该页。</span><span class="sxs-lookup"><span data-stu-id="ef811-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
