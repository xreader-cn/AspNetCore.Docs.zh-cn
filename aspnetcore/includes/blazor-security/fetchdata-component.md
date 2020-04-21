<span data-ttu-id="d0bbf-101">该`FetchData`组件演示如何：</span><span class="sxs-lookup"><span data-stu-id="d0bbf-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="d0bbf-102">预配访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-102">Provision an access token.</span></span>
* <span data-ttu-id="d0bbf-103">使用访问令牌在 *"服务器"* 应用中调用受保护的资源 API。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="d0bbf-104">该`@attribute [Authorize]`指令向 Blazor WebAssembly 授权系统指示用户必须获得授权才能访问此组件。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="d0bbf-105">*客户端*应用中的属性不会阻止在没有适当凭据的情况下调用服务器上的 API。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="d0bbf-106">*服务器*应用还必须在适当的终结点`[Authorize]`上使用才能正确保护它们。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="d0bbf-107">`AuthenticationService.RequestAccessToken();`负责请求可添加到调用 API 的请求中的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="d0bbf-108">如果令牌被缓存，或者服务能够在没有用户交互的情况下预配新的访问令牌，则令牌请求将成功。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="d0bbf-109">否则，令牌请求将失败。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="d0bbf-110">为了获得要包含在请求中的实际令牌，应用必须通过调用`tokenResult.TryGetToken(out var token)`来检查请求是否成功。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="d0bbf-111">如果请求成功，则令牌变量将填充访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="d0bbf-112">令牌`Value`的属性公开要包含在请求标头中的`Authorization`文本字符串。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="d0bbf-113">如果请求失败，因为如果没有用户交互无法预配令牌，则令牌结果包含重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="d0bbf-114">导航到此 URL 会将用户带到登录页，并在身份验证成功后返回当前页面。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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

<span data-ttu-id="d0bbf-115">有关详细信息，请参阅[在身份验证操作之前保存应用状态](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)。</span><span class="sxs-lookup"><span data-stu-id="d0bbf-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
