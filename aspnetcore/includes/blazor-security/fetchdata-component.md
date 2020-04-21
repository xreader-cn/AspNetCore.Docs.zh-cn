该`FetchData`组件演示如何：

* 预配访问令牌。
* 使用访问令牌在 *"服务器"* 应用中调用受保护的资源 API。

该`@attribute [Authorize]`指令向 Blazor WebAssembly 授权系统指示用户必须获得授权才能访问此组件。 *客户端*应用中的属性不会阻止在没有适当凭据的情况下调用服务器上的 API。 *服务器*应用还必须在适当的终结点`[Authorize]`上使用才能正确保护它们。

`AuthenticationService.RequestAccessToken();`负责请求可添加到调用 API 的请求中的访问令牌。 如果令牌被缓存，或者服务能够在没有用户交互的情况下预配新的访问令牌，则令牌请求将成功。 否则，令牌请求将失败。

为了获得要包含在请求中的实际令牌，应用必须通过调用`tokenResult.TryGetToken(out var token)`来检查请求是否成功。 

如果请求成功，则令牌变量将填充访问令牌。 令牌`Value`的属性公开要包含在请求标头中的`Authorization`文本字符串。

如果请求失败，因为如果没有用户交互无法预配令牌，则令牌结果包含重定向 URL。 导航到此 URL 会将用户带到登录页，并在身份验证成功后返回当前页面。

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

有关详细信息，请参阅[在身份验证操作之前保存应用状态](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)。
