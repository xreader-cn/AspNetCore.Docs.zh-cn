<span data-ttu-id="2b058-101">`Authentication` 组件 (`Pages/Authentication.razor`) 生成的页面定义了处理各种身份验证阶段所需的路由。</span><span class="sxs-lookup"><span data-stu-id="2b058-101">The page produced by the `Authentication` component (`Pages/Authentication.razor`) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="2b058-102"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 组件：</span><span class="sxs-lookup"><span data-stu-id="2b058-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="2b058-103">由 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="2b058-103">Is provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="2b058-104">管理在每个身份验证阶段执行适当的操作。</span><span class="sxs-lookup"><span data-stu-id="2b058-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
