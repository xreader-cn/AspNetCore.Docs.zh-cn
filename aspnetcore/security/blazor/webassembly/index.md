---
title: 保护 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 了解如何将 Blazor WebAssemlby 应用作为单页应用程序 (SPA) 进行保护。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: b82341f3d1d0665d4ee31bb6d967ccf305bae9c3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661781"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>保护 ASP.NET Core Blazor WebAssembly

作者：[Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor WebAssembly 应用与单页应用程序 (SPA) 的保护方式相同。 可通过多种方式向 SPA 进行用户身份验证，但最常用、最全面的方式是使用基于 [OAuth 2.0 协议](https://oauth.net/)的实现，例如 [Open ID Connect (OIDC)](https://openid.net/connect/)。

## <a name="authentication-library"></a>身份验证库

Blazor WebAssembly 支持通过 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 库使用 OIDC 对应用进行身份验证和授权。 该库提供一组基元，可用于对 ASP.NET Core 后端进行无缝身份验证。 该库将 ASP.NET Core 标志与以[标识服务器](https://identityserver.io/)为基础的 API 身份验证集成。 该库可以针对支持 OIDC 的任何第三方标识提供者 (IP)，即 OpenID 提供者 (OP)，进行身份验证。

Blazor WebAssembly 中的身份验证支持建立在 *oidc-client.js* 库的基础之上，该库用于处理底层身份验证协议详细信息。

还有对 SPA 进行身份验证的其他选项，例如使用 SameSite cookie。 但是，Blazor WebAssembly 的工程设计决定，OAuth 和 OIDC 是在 Blazor WebAssembly 应用中进行身份验证的最佳选择。 出于以下功能和安全原因，选择了以 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 为基础的[基于令牌的身份验证](xref:security/anti-request-forgery#token-based-authentication)而不是[基于 cookie 的身份验证](xref:security/anti-request-forgery#cookie-based-authentication)：

* 使用基于令牌的协议可以减小攻击面，因为并非所有请求中都会发送令牌。
* 服务器终结点不要求针对[跨站点请求伪造 (CSRF)](xref:security/anti-request-forgery) 进行保护，因为会显式发送令牌。 因此，可以将 Blazor WebAssembly 应用与 MVC 或 Razor Pages 应用托管在同一位置。
* 令牌的权限比 cookie 窄。 例如，令牌不能用于管理用户帐户或更改用户密码，除非显式实现了此类功能。
* 令牌的生命周期更短（默认为一小时），这限制了攻击时间窗口。 还可随时撤销令牌。
* 自包含 JWT 向客户端和服务器提供身份验证进程保证。 例如，客户端可以检测和验证它收到的令牌是否合法，以及是否是在给定身份验证过程中发出的。 如果有第三方尝试在身份验证进程中偷换令牌，客户端可以检测被偷换的令牌并避免使用它。
* OAuth 和 OIDC 的令牌不依赖于用户代理行为正确以确保应用安全。
* 基于令牌的协议（例如 OAuth 和 OIDC）允许用同一组安全特征对托管和独立应用进行验证和授权。

## <a name="authentication-process-with-oidc"></a>使用 OIDC 的身份验证进程

`Microsoft.AspNetCore.Components.WebAssembly.Authentication` 库提供几个基元，用于实现使用 OIDC 的身份验证和授权。 从广义上说来，身份验证的原理如下：

* 当匿名用户选择登录按钮或请求应用了 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性的页面时，会将其重定向到应用的登录页 (`/authentication/login`)。
* 在登录页上，身份验证库准备重定向到授权终结点。 授权终结点在 Blazor WebAssembly 应用外部，可托管在独立的源。 该终结点负责确定用户是否通过身份验证，并发送一个或更多令牌作为响应。 身份验证库提供登录回叫以接收身份验证响应。
  * 如果用户未通过身份验证，会将其重定向到底层身份验证系统，通常是 ASP.NET Core 标识。
  * 如果用户已通过身份验证，则授权终结点会生成相应的令牌，并将浏览器重定向回登录回叫终结点 (`/authentication/login-callback`)。
* 当 Blazor WebAssembly 应用加载登录回叫终结点 (`/authentication/login-callback`) 时，就处理了身份验证进程。
  * 如果身份验证进程成功完成，则用户通过身份验证，可以选择返回该用户请求的原受保护 URL。
  * 如果身份验证进程由于任何原因而失败，会将用户导向登录失败页 (`/authentication/login-failed`)，并显示错误。

## <a name="support-prerendering-with-authentication"></a>支持预呈现身份验证

遵循任一托管 Blazor WebAssembly 应用主题中的指导后，请按照以下说明创建应用：

* 预呈现不需要授权的路径。
* 不预呈现需要授权的路径。

在客户端应用的 `Program` 类 (*Program.cs*) 中，将常见服务注册组织为单独的方法（例如 `ConfigureCommonServices`）：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

在服务器应用的 `Startup.ConfigureServices` 中，注册以下附加服务：

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

在服务器应用的 `Startup.Configure` 方法中，将 `endpoints.MapFallbackToFile("index.html")` 替换为 `endpoints.MapFallbackToPage("/_Host")`：

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

在服务器应用中，如果不存在 Pages  文件夹，则创建它。 在服务器应用的 Pages 文件夹中创建 _Host.cshtml 页面。   将客户端应用 wwwroot/index.html 文件中的内容粘贴到 Pages/_Host.cshtml 文件中。   更新文件的内容：

* 将 `@page "_Host"` 添加到文件顶部。
* 将 `<app>Loading...</app>` 标记替换为以下内容：

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>用于托管应用和第三方登录提供程序的选项

使用第三方提供程序对托管的 Blazor WebAssembly 应用进行身份验证和授权时，有几个选项可用于对用户进行身份验证。 选择哪一种选项取决于方案。

有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>对用户进行身份验证，以仅调用受保护的第三方 API

使用针对第三方 API 提供程序的客户端 OAuth 流对用户进行身份验证：

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 在本方案中：

* 托管应用的服务器不会发挥作用。
* 无法保护服务器上的 API。
* 应用只能调用受保护的第三方 API。

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>使用第三方提供程序对用户进行身份验证，并在主机服务器和第三方调用受保护的 API

使用第三方登录提供程序配置标识。 获取第三方 API 访问所需的令牌并进行存储。

当用户登录时，标识将在身份验证过程中收集访问和刷新令牌。 此时，可通过几种方法向第三方 API 进行 API 调用。

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>使用服务器访问令牌检索第三方访问令牌

使用服务器上生成的访问令牌从服务器 API 终结点检索第三方访问令牌。 在此处，使用第三方访问令牌直接从客户端上的标识调用第三方 API 资源。

我们不建议使用此方法。 此方法需要将第三方访问令牌视为针对公共客户端生成。 在 OAuth 范畴，公共应用没有客户端机密，因为不能信任此类应用可以安全地存储机密，将为机密客户端生成访问令牌。 机密客户端具有客户端机密，并且假定能够安全地存储机密。

* 第三方访问令牌可能会被授予其他作用域，以便基于第三方为更受信任的客户端发出令牌的情况执行敏感操作。
* 同样，不应向不受信任的客户端颁发刷新令牌，因为这样做会给客户端提供无限制的访问权限，除非存在其他限制。

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>从客户端向服务器 API 发出 API 调用以便调用第三方 API

从客户端向服务器 API 发出 API 调用。 从服务器中检索第三方 API 资源的访问令牌，并发出任何所需调用。

尽管此方法需要额外的网络跃点通过服务器来调用第三方 API，但最终可提供更安全的体验：

* 服务器可以存储刷新令牌，并确保应用不会失去对第三方资源的访问权限。
* 应用无法从服务器泄漏可能包含更多敏感权限的访问令牌。
