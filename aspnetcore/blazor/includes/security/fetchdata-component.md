---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: 8772f383a830936881564ca95a7f034ba08a5798
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551337"
---
`FetchData` 组件显示如何：

* 预配访问令牌。
* 使用访问令牌调用 Server 应用中受保护的资源 API。

[`@attribute [Authorize]`](xref:mvc/views/razor#attribute) 指令向 Blazor WebAssembly 授权系统发出指示，用户必须获得授权才能访问此组件。 如果 `Client` 应用中存在该属性，则在没有正确凭据的情况下，不会阻止调用服务器上的 API。 `Server` 应用还必须在相应的终结点上使用 `[Authorize]` 才能适当地保护这些终结点。

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> 负责请求可添加到请求中的访问令牌，以调用 API。 如果该令牌已缓存，或者该服务在没有用户交互的情况下能够预配新的访问令牌，则令牌请求会成功。 否则，令牌请求会失败，并出现 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>，这是在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句中捕获的。

为了获得要包含在请求中的实际令牌，应用必须通过调用 [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) 来检查请求是否成功。

如果请求成功，将使用访问令牌填充令牌变量。 此标记的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> 属性会公开要包含在 `Authorization` 请求标头中的文本字符串。

如果请求失败，因为在没有用户交互的情况下无法预配令牌，令牌结果将包含重定向 URL。 导航到此 URL 后，用户将进入登录页，并在身份验证成功后返回到当前页面。

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
