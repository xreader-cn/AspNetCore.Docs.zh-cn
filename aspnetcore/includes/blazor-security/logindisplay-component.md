<span data-ttu-id="bd3d8-101">`LoginDisplay` 组件（*shared/LoginDisplay*）在 `MainLayout` 组件（*shared/MainLayout*）中呈现并管理以下行为：</span><span class="sxs-lookup"><span data-stu-id="bd3d8-101">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="bd3d8-102">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="bd3d8-102">For authenticated users:</span></span>
  * <span data-ttu-id="bd3d8-103">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="bd3d8-103">Displays the current username.</span></span>
  * <span data-ttu-id="bd3d8-104">提供用于注销应用的按钮。</span><span class="sxs-lookup"><span data-stu-id="bd3d8-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="bd3d8-105">对于匿名用户，提供登录选项。</span><span class="sxs-lookup"><span data-stu-id="bd3d8-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
