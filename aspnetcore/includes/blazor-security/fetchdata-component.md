<span data-ttu-id="19cd5-101">此 `FetchData` 组件显示如何：</span><span class="sxs-lookup"><span data-stu-id="19cd5-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="19cd5-102">设置访问令牌。</span><span class="sxs-lookup"><span data-stu-id="19cd5-102">Provision an access token.</span></span>
* <span data-ttu-id="19cd5-103">使用访问令牌调用*服务器*应用中受保护的资源 API。</span><span class="sxs-lookup"><span data-stu-id="19cd5-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="19cd5-104">[`@attribute [Authorize]`](xref:mvc/views/razor#attribute)指令向 Blazor WebAssembly 授权系统表明，用户必须获得授权才能访问此组件。</span><span class="sxs-lookup"><span data-stu-id="19cd5-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="19cd5-105">如果*客户端*应用程序中存在该属性，则不会阻止在没有正确凭据的情况下调用服务器上的 API。</span><span class="sxs-lookup"><span data-stu-id="19cd5-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="19cd5-106">*服务器*应用程序还必须 `[Authorize]` 在适当的终结点上使用，才能正确地对其进行保护。</span><span class="sxs-lookup"><span data-stu-id="19cd5-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="19cd5-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>负责请求可添加到请求中的访问令牌，以调用 API。</span><span class="sxs-lookup"><span data-stu-id="19cd5-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="19cd5-108">如果该令牌已缓存，或者该服务在没有用户交互的情况下能够预配新的访问令牌，则令牌请求会成功。</span><span class="sxs-lookup"><span data-stu-id="19cd5-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="19cd5-109">否则，令牌请求会失败，并出现一个 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> 在[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中捕获的。</span><span class="sxs-lookup"><span data-stu-id="19cd5-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="19cd5-110">若要获取要包含在请求中的实际标记，应用必须通过调用 TokenResult 来检查请求是否成功。 [TryGetToken （out var 标记）](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A)。</span><span class="sxs-lookup"><span data-stu-id="19cd5-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [tokenResult.TryGetToken(out var token)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="19cd5-111">如果请求成功，将使用访问令牌填充令牌变量。</span><span class="sxs-lookup"><span data-stu-id="19cd5-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="19cd5-112"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>此标记的属性公开要包含在请求标头中的文本字符串 `Authorization` 。</span><span class="sxs-lookup"><span data-stu-id="19cd5-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="19cd5-113">如果请求失败，因为无法在没有用户交互的情况下进行设置，令牌结果将包含重定向 URL。</span><span class="sxs-lookup"><span data-stu-id="19cd5-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="19cd5-114">导航到此 URL 后，用户将进入登录页，并在身份验证成功后返回到当前页面。</span><span class="sxs-lookup"><span data-stu-id="19cd5-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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
