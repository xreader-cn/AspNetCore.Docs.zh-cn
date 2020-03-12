`FetchData` 组件显示了如何：

* 设置访问令牌。
* 使用访问令牌调用*服务器*应用中受保护的资源 API。

`@attribute [Authorize]` 指令向 Blazor WebAssembly 授权系统表明，用户必须获得授权才能访问此组件。 如果*客户端*应用程序中存在该属性，则不会阻止在没有正确凭据的情况下调用服务器上的 API。 *服务器*应用程序还必须在适当的终结点上使用 `[Authorize]`，才能正确地对其进行保护。

`AuthenticationService.RequestAccessToken();` 负责请求可添加到请求中的访问令牌，以调用 API。 如果该令牌已缓存，或者该服务在没有用户交互的情况下能够预配新的访问令牌，则令牌请求会成功。 否则，令牌请求会失败。

为了获得要包含在请求中的实际标记，应用程序必须通过调用 `tokenResult.TryGetToken(out var token)`来检查请求是否成功。 

如果请求成功，将使用访问令牌填充令牌变量。 此标记的 `Value` 属性公开要包含在 `Authorization` 请求标头中的文本字符串。

如果请求失败，因为无法在没有用户交互的情况下进行设置，令牌结果将包含重定向 URL。 导航到此 URL 后，用户将进入登录页，并在身份验证成功后返回到当前页面。

```razor
@page "/fetchdata"
...
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
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

有关详细信息，请参阅[在执行身份验证操作之前保存应用程序状态](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)。
