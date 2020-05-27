<span data-ttu-id="81fa5-101">`App`组件（*app.config*）类似于 `App` Blazor Server apps 中的组件：</span><span class="sxs-lookup"><span data-stu-id="81fa5-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="81fa5-102"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>组件管理将公开 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> 给应用程序的其余部分。</span><span class="sxs-lookup"><span data-stu-id="81fa5-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="81fa5-103"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>组件确保当前用户有权访问给定页面或以其他方式呈现 `RedirectToLogin` 组件。</span><span class="sxs-lookup"><span data-stu-id="81fa5-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="81fa5-104">`RedirectToLogin`组件管理将未经授权的用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="81fa5-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
