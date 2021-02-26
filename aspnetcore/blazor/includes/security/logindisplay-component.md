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
ms.openlocfilehash: bdb60608ba4bd99b24e458f0543b1f4226cadf44
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551913"
---
<span data-ttu-id="09639-101">`LoginDisplay` 组件 (`Shared/LoginDisplay.razor`) 在 `MainLayout` 组件 (`Shared/MainLayout.razor`) 中呈现并管理以下行为：</span><span class="sxs-lookup"><span data-stu-id="09639-101">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="09639-102">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="09639-102">For authenticated users:</span></span>
  * <span data-ttu-id="09639-103">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="09639-103">Displays the current username.</span></span>
  * <span data-ttu-id="09639-104">提供用于注销应用的按钮。</span><span class="sxs-lookup"><span data-stu-id="09639-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="09639-105">对于匿名用户，提供登录选项。</span><span class="sxs-lookup"><span data-stu-id="09639-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
