`App` 组件（*app.config*）类似于在 Blazor Server apps 中找到 `App` 组件：

* `CascadingAuthenticationState` 组件管理向应用程序的其余部分公开 `AuthenticationState`。
* `AuthorizeRouteView` 组件确保当前用户有权访问给定页面或呈现 `RedirectToLogin` 组件。
* `RedirectToLogin` 组件管理将未经授权的用户重定向到登录页。

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <RedirectToLogin />
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
