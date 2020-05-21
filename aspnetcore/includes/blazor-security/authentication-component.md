<span data-ttu-id="d275f-101">组件生成的页面 `Authentication` （*Pages/Authentication*）定义处理不同的身份验证阶段所需的路由。</span><span class="sxs-lookup"><span data-stu-id="d275f-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="d275f-102">`RemoteAuthenticatorView`组件：</span><span class="sxs-lookup"><span data-stu-id="d275f-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="d275f-103">由 AspNetCore 包提供。 [WebAssembly](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包。</span><span class="sxs-lookup"><span data-stu-id="d275f-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="d275f-104">管理在每个身份验证阶段执行适当的操作。</span><span class="sxs-lookup"><span data-stu-id="d275f-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
