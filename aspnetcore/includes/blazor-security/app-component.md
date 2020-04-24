组件（App.config）类似于 Blazor Server apps 中的`App`组件：*App.razor* `App`

* `CascadingAuthenticationState`组件管理将公开`AuthenticationState`给应用程序的其余部分。
* `AuthorizeRouteView`组件确保当前用户有权访问给定页面或以其他方式呈现`RedirectToLogin`组件。
* `RedirectToLogin`组件管理将未经授权的用户重定向到登录页。

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
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
