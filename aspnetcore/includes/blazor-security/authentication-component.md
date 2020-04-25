`Authentication`组件生成的页面（*Pages/Authentication*）定义处理不同的身份验证阶段所需的路由。

`RemoteAuthenticatorView`组件：

* 由 AspNetCore 包提供。 [WebAssembly](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包。
* 管理在每个身份验证阶段执行适当的操作。

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
