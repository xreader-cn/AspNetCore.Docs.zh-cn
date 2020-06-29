---
title: ASP.NET Core Blazor 身份验证和授权
author: guardrex
description: 了解 Blazor 身份验证和授权方案。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/index
ms.openlocfilehash: e905f08f867b73fc37d5fed7138256ac89811312
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402398"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="3000e-103">ASP.NET Core Blazor 身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="3000e-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="3000e-104">作者：[Steve Sanderson](https://github.com/SteveSandersonMS) 及 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3000e-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3000e-105">ASP.NET Core 支持 Blazor 应用中的安全配置和管理。</span><span class="sxs-lookup"><span data-stu-id="3000e-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="3000e-106">Blazor Server应用和 Blazor WebAssembly 应用的安全方案有所不同。</span><span class="sxs-lookup"><span data-stu-id="3000e-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="3000e-107">由于 Blazor Server应用在服务器上运行，因此授权检查可确定：</span><span class="sxs-lookup"><span data-stu-id="3000e-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="3000e-108">向用户呈现的 UI 选项（例如，用户可以使用哪些菜单条目）。</span><span class="sxs-lookup"><span data-stu-id="3000e-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="3000e-109">应用程序和组件区域的访问规则。</span><span class="sxs-lookup"><span data-stu-id="3000e-109">Access rules for areas of the app and components.</span></span>

Blazor WebAssembly<span data-ttu-id="3000e-110"> 应用在客户端上运行。</span><span class="sxs-lookup"><span data-stu-id="3000e-110"> apps run on the client.</span></span> <span data-ttu-id="3000e-111">授权仅用于确定要显示的 UI 选项。</span><span class="sxs-lookup"><span data-stu-id="3000e-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="3000e-112">由于用户可修改或绕过客户端检查，因此 Blazor WebAssembly 应用无法强制执行授权访问规则。</span><span class="sxs-lookup"><span data-stu-id="3000e-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="3000e-113">[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization) 不适用于可路由的 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="3000e-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="3000e-114">如果非可路由的 Razor 组件[嵌入在页面中](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view)，则页面的授权约定会间接影响 Razor 组件以及其余页面内容。</span><span class="sxs-lookup"><span data-stu-id="3000e-114">If a non-routable Razor component is [embedded in a page](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="3000e-115">Razor 组件中不支持 <xref:Microsoft.AspNetCore.Identity.SignInManager%601> 和 <xref:Microsoft.AspNetCore.Identity.UserManager%601>。</span><span class="sxs-lookup"><span data-stu-id="3000e-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="3000e-116">身份验证</span><span class="sxs-lookup"><span data-stu-id="3000e-116">Authentication</span></span>

Blazor<span data-ttu-id="3000e-117"> 使用现有的 ASP.NET Core 身份验证机制来确立用户的身份。</span><span class="sxs-lookup"><span data-stu-id="3000e-117"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="3000e-118">具体机制取决于 Blazor 应用是使用 Blazor WebAssembly 还是 Blazor Server托管的。</span><span class="sxs-lookup"><span data-stu-id="3000e-118">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a>Blazor WebAssembly<span data-ttu-id="3000e-119">身份验证</span><span class="sxs-lookup"><span data-stu-id="3000e-119"> authentication</span></span>

<span data-ttu-id="3000e-120">在 Blazor WebAssembly 应用中，可绕过身份验证检查，因为用户可修改所有客户端代码。</span><span class="sxs-lookup"><span data-stu-id="3000e-120">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="3000e-121">所有客户端应用程序技术都是如此，其中包括 JavaScript SPA 框架或任何操作系统的本机应用程序。</span><span class="sxs-lookup"><span data-stu-id="3000e-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="3000e-122">添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="3000e-122">Add the following:</span></span>

* <span data-ttu-id="3000e-123">应用项目文件 [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="3000e-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="3000e-124">应用的 `_Imports.razor` 文件的 `Microsoft.AspNetCore.Components.Authorization` 命名空间。</span><span class="sxs-lookup"><span data-stu-id="3000e-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="3000e-125">为处理身份验证，需实现内置或自定义 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 服务，以下几节对此进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="3000e-125">To handle authentication, implementation of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="3000e-126">有关创建应用和配置的详细信息，请参阅 <xref:blazor/security/webassembly/index>。</span><span class="sxs-lookup"><span data-stu-id="3000e-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a>Blazor Server<span data-ttu-id="3000e-127">身份验证</span><span class="sxs-lookup"><span data-stu-id="3000e-127"> authentication</span></span>

Blazor Server<span data-ttu-id="3000e-128">应用通过使用 SignalR 创建的实时连接运行。</span><span class="sxs-lookup"><span data-stu-id="3000e-128"> apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="3000e-129">建立连接后，将处理[基于 SignalR 的应用的身份验证](xref:signalr/authn-and-authz)。</span><span class="sxs-lookup"><span data-stu-id="3000e-129">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="3000e-130">可以基于 cookie 或一些其他持有者令牌进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3000e-130">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="3000e-131">有关创建应用和配置的详细信息，请参阅 <xref:blazor/security/server/index>。</span><span class="sxs-lookup"><span data-stu-id="3000e-131">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="3000e-132">AuthenticationStateProvider 服务</span><span class="sxs-lookup"><span data-stu-id="3000e-132">AuthenticationStateProvider service</span></span>

<span data-ttu-id="3000e-133">内置的 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 服务可从 ASP.NET Core 的 `HttpContext.User` 获取身份验证状态数据。</span><span class="sxs-lookup"><span data-stu-id="3000e-133">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="3000e-134">身份验证状态就是这样与现有 ASP.NET Core 身份验证机制集成。</span><span class="sxs-lookup"><span data-stu-id="3000e-134">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="3000e-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 是 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 组件和 <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 组件用于获取身份验证状态的基础服务。</span><span class="sxs-lookup"><span data-stu-id="3000e-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="3000e-136">通常不直接使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>。</span><span class="sxs-lookup"><span data-stu-id="3000e-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="3000e-137">使用本文后面介绍的 [`AuthorizeView` 组件](#authorizeview-component) 或 [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) 方法。</span><span class="sxs-lookup"><span data-stu-id="3000e-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="3000e-138">直接使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 的主要缺点是，如果基础身份验证状态数据发生更改，不会自动通知组件。</span><span class="sxs-lookup"><span data-stu-id="3000e-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="3000e-139"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 服务可以提供当前用户的 <xref:System.Security.Claims.ClaimsPrincipal> 数据，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="3000e-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="3000e-140">由于用户是 <xref:System.Security.Claims.ClaimsPrincipal>，如果 `user.Identity.IsAuthenticated` 为 `true`，可以枚举声明并评估角色成员身份。</span><span class="sxs-lookup"><span data-stu-id="3000e-140">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="3000e-141">有关依赖关系注入 (DI) 和服务的详细信息，请参阅<xref:blazor/fundamentals/dependency-injection>和 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="3000e-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="3000e-142">现自定义 AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="3000e-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="3000e-143">如果应用需要自定义提供程序，请实现 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 并替代 `GetAuthenticationStateAsync`：</span><span class="sxs-lookup"><span data-stu-id="3000e-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="3000e-144">在 Blazor WebAssembly 应用中，`CustomAuthStateProvider` 服务已在 `Program.cs` 的 `Main` 中注册：</span><span class="sxs-lookup"><span data-stu-id="3000e-144">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="3000e-145">在 Blazor Server应用中，`CustomAuthStateProvider` 服务已在 `Startup.ConfigureServices` 中注册：</span><span class="sxs-lookup"><span data-stu-id="3000e-145">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="3000e-146">使用以上示例中的 `CustomAuthStateProvider`，通过用户名 `mrfibuli` 对所有用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3000e-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="3000e-147">公开身份验证状态作为级联参数</span><span class="sxs-lookup"><span data-stu-id="3000e-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="3000e-148">如果过程逻辑需要身份验证状态数据（如在执行用户触发的操作时），请通过定义类型为 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 的级联参数来获取身份验证状态数据：</span><span class="sxs-lookup"><span data-stu-id="3000e-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="3000e-149">如果 `user.Identity.IsAuthenticated` 为 `true`，可以枚举声明并评估角色成员身份。</span><span class="sxs-lookup"><span data-stu-id="3000e-149">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="3000e-150">使用 `App` 组件 (`App.razor`) 中的 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 和 <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 组件来设置 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 级联参数：</span><span class="sxs-lookup"><span data-stu-id="3000e-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="3000e-151">在 Blazor WebAssembly 应用中，将选项和授权服务添加到 `Program.Main`：</span><span class="sxs-lookup"><span data-stu-id="3000e-151">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="3000e-152">在 Blazor Server应用中，已有选项和授权服务，因此无需进一步操作。</span><span class="sxs-lookup"><span data-stu-id="3000e-152">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="3000e-153">授权</span><span class="sxs-lookup"><span data-stu-id="3000e-153">Authorization</span></span>

<span data-ttu-id="3000e-154">对用户进行身份验证后，应用授权规则来控制用户可以执行的操作。</span><span class="sxs-lookup"><span data-stu-id="3000e-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="3000e-155">通常根据以下几点确定是授权访问还是拒绝访问：</span><span class="sxs-lookup"><span data-stu-id="3000e-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="3000e-156">已对用户进行身份验证（已登录）。</span><span class="sxs-lookup"><span data-stu-id="3000e-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="3000e-157">用户属于某个角色。</span><span class="sxs-lookup"><span data-stu-id="3000e-157">A user is in a *role*.</span></span>
* <span data-ttu-id="3000e-158">用户具有声明。</span><span class="sxs-lookup"><span data-stu-id="3000e-158">A user has a *claim*.</span></span>
* <span data-ttu-id="3000e-159">满足策略要求。</span><span class="sxs-lookup"><span data-stu-id="3000e-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="3000e-160">上述所有概念都与 ASP.NET Core MVC 或 Razor Pages 应用中的概念相同。</span><span class="sxs-lookup"><span data-stu-id="3000e-160">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="3000e-161">有关 ASP.NET Core 安全性的详细信息，请参阅 [ASP.NET Core 安全性和 Identity](xref:security/index) 下的文章。</span><span class="sxs-lookup"><span data-stu-id="3000e-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="3000e-162">AuthorizeView 组件</span><span class="sxs-lookup"><span data-stu-id="3000e-162">AuthorizeView component</span></span>

<span data-ttu-id="3000e-163"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 组件根据用户是否有权查看来选择性地显示 UI。</span><span class="sxs-lookup"><span data-stu-id="3000e-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="3000e-164">如果只需要为用户显示数据，而不需要在过程逻辑中使用用户的标识，那么此方法很有用。</span><span class="sxs-lookup"><span data-stu-id="3000e-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="3000e-165">该组件公开了一个 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> 类型的 `context` 变量，可以使用该变量来访问有关已登录用户的信息：</span><span class="sxs-lookup"><span data-stu-id="3000e-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="3000e-166">另外，如果用户未经过身份验证，你还可以提供不同的内容以供显示：</span><span class="sxs-lookup"><span data-stu-id="3000e-166">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="3000e-167">可以在 `NavMenu` 组件 (`Shared/NavMenu.razor`) 中使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 组件来显示 [`NavLink` 组件](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) 的列表项 (`<li>...</li>`)，但请注意，此方法仅从呈现的输出中删除列表项。</span><span class="sxs-lookup"><span data-stu-id="3000e-167">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="3000e-168">它不会阻止用户导航到该组件。</span><span class="sxs-lookup"><span data-stu-id="3000e-168">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="3000e-169">`<Authorized>` 和 `<NotAuthorized>` 标记的内容可以包括任意项，如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="3000e-169">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="3000e-170">[授权](#authorization)一节中介绍了授权条件，如用于控制 UI 选项或访问权限的角色或策略。</span><span class="sxs-lookup"><span data-stu-id="3000e-170">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="3000e-171">如果未指定授权条件，则 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 使用默认策略，并且：</span><span class="sxs-lookup"><span data-stu-id="3000e-171">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="3000e-172">将经过身份验证（已登录）的用户视为已授权。</span><span class="sxs-lookup"><span data-stu-id="3000e-172">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="3000e-173">将未经过身份验证（已注销）的用户视为未授权。</span><span class="sxs-lookup"><span data-stu-id="3000e-173">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="3000e-174">基于角色和基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="3000e-174">Role-based and policy-based authorization</span></span>

<span data-ttu-id="3000e-175"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 组件支持基于角色或基于策略的授权 。</span><span class="sxs-lookup"><span data-stu-id="3000e-175">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="3000e-176">对于基于角色的授权，请使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> 参数：</span><span class="sxs-lookup"><span data-stu-id="3000e-176">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="3000e-177">有关详细信息，请参阅 <xref:security/authorization/roles>。</span><span class="sxs-lookup"><span data-stu-id="3000e-177">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="3000e-178">对于基于策略的授权，请使用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> 参数：</span><span class="sxs-lookup"><span data-stu-id="3000e-178">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="3000e-179">基于策略的授权包含一个特例，即基于声明的授权。</span><span class="sxs-lookup"><span data-stu-id="3000e-179">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="3000e-180">例如，可以定义一个要求用户具有特定声明的策略。</span><span class="sxs-lookup"><span data-stu-id="3000e-180">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="3000e-181">有关详细信息，请参阅 <xref:security/authorization/policies>。</span><span class="sxs-lookup"><span data-stu-id="3000e-181">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="3000e-182">可以在 Blazor Server应用或 Blazor WebAssembly 应用中使用这些 API。</span><span class="sxs-lookup"><span data-stu-id="3000e-182">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="3000e-183">如果 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> 或 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> 均未指定，则 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 使用默认策略。</span><span class="sxs-lookup"><span data-stu-id="3000e-183">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="3000e-184">异步身份验证期间显示的内容</span><span class="sxs-lookup"><span data-stu-id="3000e-184">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="3000e-185">通过 Blazor，可通过异步方式确定身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="3000e-185">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="3000e-186">此方法的主要应用场景是，向外部终结点发出请求来进行身份验证的 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="3000e-186">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="3000e-187">正在进行身份验证时，<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 默认情况下不显示任何内容。</span><span class="sxs-lookup"><span data-stu-id="3000e-187">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="3000e-188">若要在进行身份验证期间显示内容，请使用 `<Authorizing>` 元素：</span><span class="sxs-lookup"><span data-stu-id="3000e-188">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="3000e-189">此方法通常不适用于 Blazor Server应用。</span><span class="sxs-lookup"><span data-stu-id="3000e-189">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="3000e-190">身份验证状态一经确立，Blazor Server应用便会立即获知身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="3000e-190">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="3000e-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> 内容可以在 Blazor Server应用的 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> 组件中提供，但此内容从不显示。</span><span class="sxs-lookup"><span data-stu-id="3000e-191"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="3000e-192">[Authorize] 属性</span><span class="sxs-lookup"><span data-stu-id="3000e-192">[Authorize] attribute</span></span>

<span data-ttu-id="3000e-193">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性可以在 Razor 组件中使用：</span><span class="sxs-lookup"><span data-stu-id="3000e-193">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="3000e-194">只在通过 Blazor 路由器到达的 `@page` 组件上使用 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)。</span><span class="sxs-lookup"><span data-stu-id="3000e-194">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="3000e-195">授权仅作为路由的一个方面执行，而不是作为页面中呈现的子组件来执行。</span><span class="sxs-lookup"><span data-stu-id="3000e-195">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="3000e-196">若要授权在页面中显示特定部分，请改用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>。</span><span class="sxs-lookup"><span data-stu-id="3000e-196">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="3000e-197">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性还支持基于角色或基于策略的授权。</span><span class="sxs-lookup"><span data-stu-id="3000e-197">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="3000e-198">对于基于角色的授权，请使用 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> 参数：</span><span class="sxs-lookup"><span data-stu-id="3000e-198">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="3000e-199">对于基于策略的授权，请使用 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> 参数：</span><span class="sxs-lookup"><span data-stu-id="3000e-199">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="3000e-200">如果既没有指定 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> 也没有指定 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>，则 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 使用默认策略，此策略默认：</span><span class="sxs-lookup"><span data-stu-id="3000e-200">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="3000e-201">将经过身份验证（已登录）的用户视为已授权。</span><span class="sxs-lookup"><span data-stu-id="3000e-201">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="3000e-202">将未经过身份验证（已注销）的用户视为未授权。</span><span class="sxs-lookup"><span data-stu-id="3000e-202">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="3000e-203">使用路由器组件自定义未授权的内容</span><span class="sxs-lookup"><span data-stu-id="3000e-203">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="3000e-204"><xref:Microsoft.AspNetCore.Components.Routing.Router> 组件与 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 组件搭配使用时，可允许应用程序在以下情况下指定自定义内容：</span><span class="sxs-lookup"><span data-stu-id="3000e-204">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="3000e-205">找不到内容。</span><span class="sxs-lookup"><span data-stu-id="3000e-205">Content isn't found.</span></span>
* <span data-ttu-id="3000e-206">用户不符合应用于组件的 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 条件。</span><span class="sxs-lookup"><span data-stu-id="3000e-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="3000e-207">[`[Authorize]` 属性](#authorize-attribute)一节中介绍了 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="3000e-207">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="3000e-208">正在进行异步身份验证。</span><span class="sxs-lookup"><span data-stu-id="3000e-208">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="3000e-209">在默认的 Blazor Server项目模板中，`App` 组件 (`App.razor`) 展示了如何设置自定义内容：</span><span class="sxs-lookup"><span data-stu-id="3000e-209">In the default Blazor Server project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="3000e-210">`<NotFound>`、`<NotAuthorized>` 和 `<Authorizing>` 标记的内容可以包括任意项，如其他交互式组件。</span><span class="sxs-lookup"><span data-stu-id="3000e-210">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="3000e-211">如果未指定 `<NotAuthorized>` 元素，<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> 就会使用以下回退消息：</span><span class="sxs-lookup"><span data-stu-id="3000e-211">If the `<NotAuthorized>` element isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="3000e-212">有关身份验证状态更改的通知</span><span class="sxs-lookup"><span data-stu-id="3000e-212">Notification about authentication state changes</span></span>

<span data-ttu-id="3000e-213">如果应用确定基础身份验证状态数据已更改（例如，由于用户已注销或其他用户已更改其角色），则[自定义 `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) 可以选择对 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> 基类调用 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="3000e-213">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="3000e-214">这会通知身份验证状态数据（例如 <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>）使用者使用新数据重新呈现。</span><span class="sxs-lookup"><span data-stu-id="3000e-214">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="3000e-215">过程逻辑</span><span class="sxs-lookup"><span data-stu-id="3000e-215">Procedural logic</span></span>

<span data-ttu-id="3000e-216">如果需要应用在过程逻辑中检查授权规则，请使用类型为 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 的级联参数来获取用户的 <xref:System.Security.Claims.ClaimsPrincipal>。</span><span class="sxs-lookup"><span data-stu-id="3000e-216">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="3000e-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 可以与其他服务（如 `IAuthorizationService`）结合使用来评估策略。</span><span class="sxs-lookup"><span data-stu-id="3000e-217">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="3000e-218">在 Blazor WebAssembly 应用组件中，添加 <xref:Microsoft.AspNetCore.Authorization> 和 <xref:Microsoft.AspNetCore.Components.Authorization> 命名空间：</span><span class="sxs-lookup"><span data-stu-id="3000e-218">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="3000e-219">可以通过将这些命名空间添加到应用的 `_Imports.razor` 文件来全局提供它们。</span><span class="sxs-lookup"><span data-stu-id="3000e-219">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="3000e-220">排查错误</span><span class="sxs-lookup"><span data-stu-id="3000e-220">Troubleshoot errors</span></span>

<span data-ttu-id="3000e-221">常见错误：</span><span class="sxs-lookup"><span data-stu-id="3000e-221">Common errors:</span></span>

* <span data-ttu-id="3000e-222">**授权需要 `Task\<AuthenticationState>` 类型的级联参数。请考虑使用 `CascadingAuthenticationState` 来提供此参数。**</span><span class="sxs-lookup"><span data-stu-id="3000e-222">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="3000e-223">**对于 `authenticationStateTask`，收到了 `null` 值**</span><span class="sxs-lookup"><span data-stu-id="3000e-223">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="3000e-224">项目很可能不是使用已启用身份验证的 Blazor Server模板创建的。</span><span class="sxs-lookup"><span data-stu-id="3000e-224">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="3000e-225">使用 `<CascadingAuthenticationState>` 将 UI 树的某些部分括起来，例如下面 `App` 组件 (`App.razor`) 中所示：</span><span class="sxs-lookup"><span data-stu-id="3000e-225">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="3000e-226"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> 提供 `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` 级联参数，它反过来又从基础 <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI 服务接收此参数。</span><span class="sxs-lookup"><span data-stu-id="3000e-226">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3000e-227">其他资源</span><span class="sxs-lookup"><span data-stu-id="3000e-227">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="3000e-228">[令人惊叹的 Blazor：身份验证](https://github.com/AdrienTorris/awesome-blazor#authentication)社区示例链接</span><span class="sxs-lookup"><span data-stu-id="3000e-228">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
