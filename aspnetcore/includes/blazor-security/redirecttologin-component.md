<span data-ttu-id="72525-101">`RedirectToLogin`组件（*Shared/RedirectToLogin*）：</span><span class="sxs-lookup"><span data-stu-id="72525-101">The `RedirectToLogin` component (*Shared/RedirectToLogin.razor*):</span></span>

* <span data-ttu-id="72525-102">管理将未经授权的用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="72525-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="72525-103">保留用户尝试访问的当前 URL，以便在身份验证成功时可以将其返回到该页。</span><span class="sxs-lookup"><span data-stu-id="72525-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

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
