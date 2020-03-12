<span data-ttu-id="ab9fe-101">`App` 组件（*app.config*）类似于在 Blazor Server apps 中找到 `App` 组件：</span><span class="sxs-lookup"><span data-stu-id="ab9fe-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="ab9fe-102">`CascadingAuthenticationState` 组件管理向应用程序的其余部分公开 `AuthenticationState`。</span><span class="sxs-lookup"><span data-stu-id="ab9fe-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="ab9fe-103">`AuthorizeRouteView` 组件确保当前用户有权访问给定页面或呈现 `RedirectToLogin` 组件。</span><span class="sxs-lookup"><span data-stu-id="ab9fe-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="ab9fe-104">`RedirectToLogin` 组件管理将未经授权的用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="ab9fe-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
