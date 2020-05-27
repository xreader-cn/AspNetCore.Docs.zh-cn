此 `FetchData` 组件显示如何：

* 设置访问令牌。
* 使用访问令牌调用*服务器*应用中受保护的资源 API。

[`@attribute [Authorize]`](xref:mvc/views/razor#attribute)指令向 Blazor WebAssembly 授权系统表明，用户必须获得授权才能访问此组件。 如果*客户端*应用程序中存在该属性，则不会阻止在没有正确凭据的情况下调用服务器上的 API。 *服务器*应用程序还必须 `[Authorize]` 在适当的终结点上使用，才能正确地对其进行保护。

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>负责请求可添加到请求中的访问令牌，以调用 API。 如果该令牌已缓存，或者该服务在没有用户交互的情况下能够预配新的访问令牌，则令牌请求会成功。 否则，令牌请求会失败，并出现一个 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 在[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中捕获的。

若要获取要包含在请求中的实际标记，应用必须通过调用 TokenResult 来检查请求是否成功。 [TryGetToken （out var 标记）](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A)。

如果请求成功，将使用访问令牌填充令牌变量。 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>此标记的属性公开要包含在请求标头中的文本字符串 `Authorization` 。

如果请求失败，因为无法在没有用户交互的情况下进行设置，令牌结果将包含重定向 URL。 导航到此 URL 后，用户将进入登录页，并在身份验证成功后返回到当前页面。

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
